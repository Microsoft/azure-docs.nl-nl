---
title: Concepten - Toegang en identiteit in Azure Kubernetes Services (AKS)
description: Meer informatie over toegang en identiteit in Azure Kubernetes Service (AKS), waaronder Azure Active Directory-integratie, kubernetes op rollen gebaseerd toegangsbeheer (Kubernetes RBAC) en rollen en bindingen.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: b10d31cf069bc4f28a1597ec12160fa6ed98b8ce
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789551"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Toegangs- en identiteitsopties voor Azure Kubernetes Service (AKS)

U kunt de toegang tot Kubernetes-clusters op verschillende manieren verifiëren, autoriseren, beveiligen en beheren. 
* Met kubernetes op rollen gebaseerd toegangsbeheer (Kubernetes RBAC) kunt u gebruikers, groepen en serviceaccounts alleen toegang verlenen tot de resources die ze nodig hebben. 
* Met Azure Kubernetes Service (AKS) kunt u de beveiliging en machtigingenstructuur verder verbeteren via Azure Active Directory en Azure RBAC. 

Kubernetes RBAC en AKS helpen u bij het beveiligen van de toegang tot uw cluster en bieden alleen de minimaal vereiste machtigingen aan ontwikkelaars en operators.

In dit artikel worden de belangrijkste concepten beschreven die u helpen bij het verifiëren en toewijzen van machtigingen in AKS.

## <a name="aks-service-permissions"></a>AKS-servicemachtigingen

Bij het maken van een cluster genereert of wijzigt AKS resources die nodig zijn (zoals VM's en NIC's) om het cluster namens de gebruiker te maken en uit te voeren. Deze identiteit verschilt van de identiteitsmachtiging van het cluster, die wordt gemaakt tijdens het maken van het cluster.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>De clustermachtigingen maken en sturingssysteem

De volgende machtigingen zijn nodig voor de identiteit die het cluster maakt en gebruikt.

| Machtiging | Reden |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Vereist om de id van de schijfversleutelingsset te lezen. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Vereist voor het bijwerken van nabijheidsplaatsingsgroepen. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Vereist om toepassingsgateways te configureren en lid te worden van het subnet. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Vereist om de netwerkbeveiligingsgroep voor het subnet te configureren wanneer u een aangepast VNET gebruikt.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Vereist voor het configureren van de uitgaande openbare IP's op Standard Load Balancer. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Vereist om Log Analytics-werkruimten en Azure-bewaking voor containers te maken en bij te werken. |

### <a name="aks-cluster-identity-permissions"></a>AKS-clusteridentiteitsmachtigingen

De volgende machtigingen worden gebruikt door de AKS-clusteridentiteit, die wordt gemaakt en gekoppeld aan het AKS-cluster. Elke machtiging wordt gebruikt om de onderstaande redenen:

| Machtiging | Reden |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Vereist voor het maken van gebruikers en het gebruik van het cluster
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Vereist voor het configureren load balancer voor een LoadBalancer-service. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Vereist voor het zoeken en configureren van openbare IP's voor een LoadBalancer-service. |
| `Microsoft.Network/publicIPAddresses/join/action` | Vereist voor het configureren van openbare IP's voor een LoadBalancer-service. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Vereist voor het maken of verwijderen van beveiligingsregels voor een LoadBalancer-service. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Vereist om AzureDisks te configureren. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Vereist voor het configureren van opslagaccounts voor AzureFile of AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Vereist voor het configureren van routetabellen en routes voor knooppunten. |
| `Microsoft.Compute/virtualMachines/read` | Vereist om informatie te vinden voor virtuele machines in een VMAS, zoals zones, foutdomein, grootte en gegevensschijven. |
| `Microsoft.Compute/virtualMachines/write` | Vereist om AzureDisks te koppelen aan een virtuele machine in een VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Vereist om informatie te vinden voor virtuele machines in een virtuele-machineschaalset, zoals zones, foutdomein, grootte en gegevensschijven. |
| `Microsoft.Network/networkInterfaces/write` | Vereist om een virtuele machine in een VMAS toe te voegen aan load balancer back-load balancer adresgroep. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Vereist om een virtuele-machineschaalset toe te voegen aan load balancer back-load balancer adresgroepen en knooppunten uit te schalen in een virtuele-machineschaalset. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Vereist om AzureDisks te koppelen en een virtuele machine toe te voegen vanuit een virtuele-machineschaalset aan de load balancer. |
| `Microsoft.Network/networkInterfaces/read` | Vereist om interne IP-adressen te doorzoeken load balancer back-endadresgroepen te zoeken naar virtuele machines in een VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Vereist om interne IP-adressen te doorzoeken en back-load balancer te zoeken naar een virtuele machine in een virtuele-machineschaalset. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Vereist om openbare IP's te vinden voor een virtuele machine in een virtuele-machineschaalset. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Vereist om te controleren of er een subnet bestaat voor de interne load balancer in een andere resourcegroep. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Vereist voor het configureren van momentopnamen voor AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Vereist om de grootte van virtuele machines te vinden voor het vinden van AzureDisk-volumelimieten. |

### <a name="additional-cluster-identity-permissions"></a>Aanvullende clusteridentiteitsmachtigingen

Wanneer u een cluster met specifieke kenmerken maakt, hebt u de volgende aanvullende machtigingen nodig voor de clusteridentiteit. Omdat deze machtigingen niet automatisch worden toegewezen, moet u ze toevoegen aan de clusteridentiteit nadat deze is gemaakt.

| Machtiging | Reden |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Vereist als u een netwerkbeveiligingsgroep in een andere resourcegroep gebruikt. Vereist voor het configureren van beveiligingsregels voor een LoadBalancer-service. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Vereist als u een subnet in een andere resourcegroep gebruikt, zoals een aangepast VNET. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Vereist als u een subnet gebruikt dat is gekoppeld aan een routetabel in een andere resourcegroep, zoals een aangepast VNET met een aangepaste routetabel. Vereist om te controleren of er al een subnet bestaat voor het subnet in de andere resourcegroep. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Vereist als u een intern load balancer in een andere resourcegroep. Vereist om te controleren of er al een subnet bestaat voor de interne load balancer in de resourcegroep. |
| `Microsoft.Network/privatednszones/*` | Vereist als u een privé-DNS-zone in een andere resourcegroep gebruikt, zoals een aangepaste privateDNSZone. |

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC biedt gedetailleerde filtering van gebruikersacties. Met dit besturingsmechanisme:
* U wijst machtigingen voor gebruikers of gebruikersgroepen toe om resources te maken en te wijzigen of logboeken van het uitvoeren van toepassingsworkloads weer te geven. 
* U kunt het bereik van machtigingen voor één naamruimte of voor het hele AKS-clusterbereiken. 
* U maakt *rollen om* machtigingen te definiëren en wijst deze rollen vervolgens toe aan gebruikers met *rolbindingen.*

Zie Using [Kubernetes RBAC authorization (Kubernetes RBAC-autorisatie gebruiken) voor meer informatie.][kubernetes-rbac]

### <a name="roles-and-clusterroles"></a>Rollen en ClusterRollen

#### <a name="roles"></a>Rollen
Voordat u machtigingen toewijst aan gebruikers met Kubernetes RBAC, definieert u gebruikersmachtigingen *als* rol . Verleen machtigingen binnen een naamruimte met behulp van rollen. 

> [!NOTE]
> Kubernetes-rollen *verlenen* machtigingen; ze weigeren *geen* machtigingen.

Als u machtigingen wilt verlenen voor het hele cluster of voor clusterbronnen buiten een bepaalde naamruimte, kunt u in plaats daarvan *ClusterRoles gebruiken.*

#### <a name="clusterroles"></a>ClusterRoles

Een ClusterRole verleent en past machtigingen toe op resources in het hele cluster, niet op een specifieke naamruimte.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings en ClusterRoleBindings

Zodra u rollen hebt gedefinieerd om machtigingen te verlenen aan resources, wijst u deze Kubernetes RBAC-machtigingen toe met een *RoleBinding.* Als uw AKS-cluster is geïntegreerd [met Azure Active Directory (Azure AD),](#azure-ad-integration)verleent RoleBindings machtigingen aan Azure AD-gebruikers om acties in het cluster uit te voeren. Zie Toegang tot clusterbronnen beheren met behulp van [op kubernetes-rollen](azure-ad-rbac.md)gebaseerd toegangsbeheer en Azure Active Directory identiteiten.

#### <a name="rolebindings"></a>RoleBindings

Wijs rollen toe aan gebruikers voor een bepaalde naamruimte met behulp van RoleBindings. Met RoleBindings kunt u één AKS-cluster logisch scheiden, zodat gebruikers alleen toegang hebben tot de toepassingsresources in hun toegewezen naamruimte. 

Als u rollen wilt binden in het hele cluster of aan clusterresources buiten een bepaalde naamruimte, gebruikt u in plaats *daarvan ClusterRoleBindings.*

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Met een ClusterRoleBinding verbindt u rollen met gebruikers en is u van toepassing op resources in het hele cluster, niet op een specifieke naamruimte. Met deze aanpak kunt u beheerders of ondersteuningstechnici toegang verlenen tot alle resources in het AKS-cluster.


> [!NOTE]
> Microsoft/AKS voert clusteracties uit met toestemming van de gebruiker onder een ingebouwde Kubernetes-rol en `aks-service` ingebouwde rolbinding `aks-service-rolebinding` . 
> 
> Met deze rol kan AKS problemen met clusters oplossen en diagnosticeren, maar kunnen machtigingen niet worden gewijzigd en kunnen er geen rollen, rolbindingen of andere acties met hoge bevoegdheden worden gebruikt. Roltoegang is alleen ingeschakeld onder actieve ondersteuningstickets met JIT-toegang (Just-In-Time). Meer informatie over [AKS-ondersteuningsbeleid.](support-policies.md)


### <a name="kubernetes-service-accounts"></a>Kubernetes-serviceaccounts

*Serviceaccounts* zijn een van de primaire gebruikerstypen in Kubernetes. De Kubernetes-API bevat en beheert serviceaccounts. Serviceaccountreferenties worden opgeslagen als Kubernetes-geheimen, zodat ze kunnen worden gebruikt door geautoriseerde pods om te communiceren met de API-server. De meeste API-aanvragen bieden een verificatie-token voor een serviceaccount of een normaal gebruikersaccount.

Normale gebruikersaccounts bieden meer traditionele toegang voor menselijke beheerders of ontwikkelaars, niet alleen voor services en processen. Kubernetes biedt geen oplossing voor identiteitsbeheer voor het opslaan van gewone gebruikersaccounts en wachtwoorden, maar u kunt externe identiteitsoplossingen integreren in Kubernetes. Voor AKS-clusters is deze geïntegreerde identiteitsoplossing Azure AD.

Zie Kubernetes-verificatie voor meer informatie over de identiteitsopties in [Kubernetes.][kubernetes-authentication]

## <a name="azure-ad-integration"></a>Azure AD-integratie

Verbeter de beveiliging van uw AKS-cluster met Azure AD-integratie. Azure AD is gebouwd op tientallen jaren zakelijk identiteitsbeheer en is een multiten tenant adreslijst- en identiteitsbeheerservice in de cloud die kerndirectoryservices, toegangsbeheer voor toepassingen en identiteitsbeveiliging combineert. Met Azure AD kunt u on-premises identiteiten integreren in AKS-clusters om één bron te bieden voor accountbeheer en -beveiliging.

![Azure Active Directory integratie met AKS-clusters](media/concepts-identity/aad-integration.png)

Met met Azure AD geïntegreerde AKS-clusters kunt u gebruikers of groepen toegang verlenen tot Kubernetes-resources binnen een naamruimte of in het cluster. 

1. Om een `kubectl` configuratiecontext te verkrijgen, voert een gebruiker de [opdracht az aks get-credentials][az-aks-get-credentials] uit. 
1. Wanneer een gebruiker interactie heeft met het AKS-cluster met , wordt hij gevraagd zich aan te `kubectl` melden met zijn Azure AD-referenties. 

Deze aanpak biedt één bron voor het beheer van gebruikersaccounts en wachtwoordreferenties. De gebruiker heeft alleen toegang tot de resources zoals gedefinieerd door de clusterbeheerder.

Azure AD-verificatie wordt geleverd aan AKS-clusters met OpenID Connect. OpenID Connect is een identiteitslaag die is gebouwd op het OAuth 2.0-protocol. Zie de Open ID Connect-OpenID Connect meer informatie over [het maken van een verbinding.][openid-connect] Vanuit het Kubernetes-cluster wordt [webhooktokenverificatie][webhook-token-docs] gebruikt om verificatietokens te verifiëren. Verificatie van webhook-token wordt geconfigureerd en beheerd als onderdeel van het AKS-cluster.

### <a name="webhook-and-api-server"></a>Webhook en API-server

![Verificatiestroom voor webhook en API-server](media/concepts-identity/auth-flow.png)

Zoals u in de bovenstaande afbeelding kunt zien, roept de API-server de AKS-webhookserver aan en voert de volgende stappen uit:

1. `kubectl` gebruikt de Azure AD-clienttoepassing om gebruikers aan te melden met [de OAuth 2.0-stroom](../active-directory/develop/v2-oauth2-device-code.md)voor het verlenen van apparaatautorisatie.
2. Azure AD biedt een access_token, id_token en een refresh_token.
3. De gebruiker doet een aanvraag bij `kubectl` met een access_token van `kubeconfig` .
4. `kubectl` verzendt de access_token naar API Server.
5. De API-server is geconfigureerd met de auth WebHook-server om validatie uit te voeren.
6. De verificatiewebhookserver bevestigt dat JSON Web Token handtekening geldig is door de openbare ondertekeningssleutel van Azure AD te controleren.
7. De servertoepassing gebruikt door de gebruiker opgegeven referenties om groepslidmaatschap van de aangemelde gebruiker op te vragen vanuit de MS-Graph API.
8. Er wordt een antwoord verzonden naar de API-server met gebruikersgegevens zoals de upn-claim (user principal name) van het toegangsteken en het groepslidmaatschap van de gebruiker op basis van de object-id.
9. De API voert een autorisatiebeslissing uit op basis van de Kubernetes Role/RoleBinding.
10. Zodra de API-server is geautoriseerd, retourneert deze een antwoord op `kubectl` .
11. `kubectl` geeft feedback aan de gebruiker.
 
Leer hoe u AKS integreert met Azure AD met onze [door AKS beheerde handleiding voor Azure AD-integratie.](managed-aad.md)

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Op rollen gebaseerd toegangsbeheer (RBAC) van Azure is een autorisatiesysteem dat is gebouwd op [Azure Resource Manager](../azure-resource-manager/management/overview.md) dat een fijnkeurig toegangsbeheer van Azure-resources biedt.

| RBAC-systeem | Description |
|---|---|
| Kubernetes RBAC | Ontworpen om te werken met Kubernetes-resources binnen uw AKS-cluster. |
| Azure RBAC | Ontworpen om te werken met resources binnen uw Azure-abonnement. |

Met Azure RBAC maakt u een *roldefinitie* die een overzicht geeft van de machtigingen die moeten worden toegepast. Vervolgens wijst u een gebruiker of groep deze roldefinitie toe via een *roltoewijzing* voor een bepaald *bereik.* Het bereik kan een afzonderlijke resource, een resourcegroep of binnen het abonnement zijn.

Zie Wat is op rollen gebaseerd toegangsbeheer van [Azure (Azure RBAC)?][azure-rbac] voor meer informatie.

Er zijn twee toegangsniveaus nodig om een AKS-cluster volledig te kunnen gebruiken: 
* [Toegang tot de AKS-resource in uw Azure-abonnement](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * Beheer het schalen of upgraden van uw cluster met behulp van de AKS-API's.
  * Haal uw `kubeconfig` op.
* Toegang tot de Kubernetes-API. Deze toegang wordt beheerd door:
  * [Kubernetes RBAC](#kubernetes-rbac) (traditioneel).
  * [Azure RBAC integreren met AKS voor Kubernetes-autorisatie.](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC om toegang tot de AKS-resource te autoreren

Met Azure RBAC kunt u uw gebruikers (of identiteiten) gedetailleerde toegang bieden tot AKS-resources in een of meer abonnementen. U kunt bijvoorbeeld de rol inzender [Azure Kubernetes Service gebruiken](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) om uw cluster te schalen en bij te upgraden. Ondertussen heeft een andere gebruiker met de [Azure Kubernetes Service rol Clusterbeheerder](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) alleen toestemming om de beheerder op te `kubeconfig` halen.

U kunt uw gebruiker ook de algemene rol [Inzender](../role-based-access-control/built-in-roles.md#contributor) geven. Met de algemene rol Inzender kunnen gebruikers de bovenstaande machtigingen en elke mogelijke actie uitvoeren op de AKS-resource, behalve het beheren van machtigingen.

[Gebruik Azure RBAC om toegang tot het Kubernetes-configuratiebestand in AKS te definiëren.](control-kubeconfig-access.md)

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC voor Kubernetes-autorisatie (preview)

Met de Azure RBAC-integratie maakt AKS gebruik van een Kubernetes Authorization-webhookserver, zodat u met Azure AD geïntegreerde Kubernetes-clusterresourcemachtigingen en -toewijzingen kunt beheren met behulp van Azure-roldefinitie en roltoewijzingen.

![Azure RBAC voor Kubernetes-autorisatiestroom](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Zoals u in het bovenstaande diagram kunt zien, volgen bij het gebruik van de Azure RBAC-integratie alle aanvragen voor de Kubernetes-API dezelfde verificatiestroom als wordt uitgelegd in de sectie [Azure Active Directory-integratie.](#azure-ad-integration) 

Als de identiteit die de aanvraag maakt, bestaat in Azure AD, zal Azure een team met Kubernetes RBAC gebruiken om de aanvraag te autoreren. Als de identiteit buiten Azure AD bestaat (dat wil zeggen een Kubernetes-serviceaccount), wordt de autorisatie naar de normale Kubernetes RBAC uitgesteld.

In dit scenario gebruikt u Azure RBAC-mechanismen en API's om ingebouwde rollen aan gebruikers toe te wijzen of om aangepaste rollen te maken, net zoals u zou doen met Kubernetes-rollen. 

Met deze functie geeft u gebruikers niet alleen machtigingen voor de AKS-resource voor alle abonnementen, maar configureert u ook de rol en machtigingen voor binnen elk van deze clusters waarmee de toegang tot de Kubernetes-API wordt gecontroleerd. U kunt de rol bijvoorbeeld verlenen `Azure Kubernetes Service RBAC Viewer` voor het abonnementsbereik. De ontvanger van de rol kan alle Kubernetes-objecten van alle clusters in een lijst en op halen zonder deze te wijzigen.

> [!IMPORTANT]
> U moet Azure RBAC inschakelen voor Kubernetes-autorisatie voordat u deze functie gebruikt. Volg onze handleiding [Azure RBAC voor Kubernetes-autorisatie](manage-azure-rbac.md) gebruiken voor meer informatie en stapsgewijs advies.

#### <a name="built-in-roles"></a>Ingebouwde rollen

AKS biedt de volgende vier ingebouwde rollen. Ze zijn vergelijkbaar met de [ingebouwde Kubernetes-rollen](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) met enkele verschillen, zoals ondersteunende CRD's. Bekijk de volledige lijst met acties die zijn toegestaan door elke [ingebouwde Azure-rol.](../role-based-access-control/built-in-roles.md)

| Rol                                | Beschrijving  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC-viewer  | Staat alleen-lezentoegang toe om de meeste objecten in een naamruimte te zien. <br> Het weergeven van rollen of rolbindingen is niet toegestaan.<br> Weergave is niet `Secrets` toegestaan. Als u de inhoud leest, krijgt u toegang tot referenties in de naamruimte, waardoor API-toegang mogelijk is zoals elke in de naamruimte `Secrets` `ServiceAccount` `ServiceAccount` (een vorm van escalatie van bevoegdheden).  |
| Azure Kubernetes Service RBAC Writer | Hiermee staat u lees-/schrijftoegang toe tot de meeste objecten in een naamruimte. <br> Het weergeven of wijzigen van rollen of rolbindingen is niet toegestaan. <br> Hiermee kunt u pods openen en uitvoeren als elk ServiceAccount in de naamruimte, zodat deze kan worden gebruikt om de API-toegangsniveaus van elk ServiceAccount in de `Secrets` naamruimte te verkrijgen. |
| Azure Kubernetes Service RBAC-beheerder  | Hiermee staat u beheerderstoegang toe, bedoeld om te worden verleend binnen een naamruimte. <br> Hiermee staat u lees-/schrijftoegang toe tot de meeste resources in een naamruimte (of clusterbereik), met inbegrip van de mogelijkheid om rollen en rolbindingen binnen de naamruimte te maken. <br> Staat geen schrijftoegang toe tot resourcequota of tot de naamruimte zelf. |
| Azure Kubernetes Service RBAC-clusterbeheerder  | Hiermee staat u supergebruikerstoegang toe om acties uit te voeren op elke resource. <br> Geeft volledige controle over elke resource in het cluster en in alle naamruimten. |


## <a name="summary"></a>Samenvatting

Bekijk de tabel voor een kort overzicht van hoe gebruikers zich kunnen verifiëren bij Kubernetes wanneer Azure AD-integratie is ingeschakeld. In alle gevallen is de volgorde van de opdrachten van de gebruiker:
1. Voer uit `az login` om te verifiëren bij Azure.
1. Voer `az aks get-credentials` uit om referenties voor het cluster te downloaden naar `.kube/config` .
1. Opdrachten `kubectl` uitvoeren. 
   * De eerste opdracht kan verificatie op basis van een browser activeren om te verifiëren bij het cluster, zoals beschreven in de volgende tabel.

In de Azure Portal vindt u het volgende:
* De *rolverkenning* (Azure RBAC-rolverkenning) die in de tweede kolom wordt genoemd, wordt weergegeven op **Access Control** tabblad. 
* De Azure AD-groep Clusterbeheerder wordt weergegeven op het **tabblad** Configuratie.
  * U vindt deze ook met parameternaam `--aad-admin-group-object-ids` in de Azure CLI.


| Description        | Rol verlenen vereist| Clusterbeheerder Azure AD-groep(s) | Wanneer gebruikt u dit? |
| -------------------|------------|----------------------------|-------------|
| Verouderde beheerdersmelding met behulp van clientcertificaat| **Azure Kubernetes-beheerdersrol**. Deze rol kan worden gebruikt met de vlag , waarmee een verouderd `az aks get-credentials` `--admin` [(niet-Azure AD)-clusterbeheerderscertificaat](control-kubeconfig-access.md) wordt gedownload naar de van de `.kube/config` gebruiker. Dit is het enige doel van 'Azure Kubernetes-beheerdersrol'.|n.v.t.|Als u permanent wordt geblokkeerd door geen toegang te hebben tot een geldige Azure AD-groep met toegang tot uw cluster.| 
| Azure AD met handmatige (cluster)RoleBindings| **Azure Kubernetes-gebruikersrol**. De rol Gebruiker kan `az aks get-credentials` worden gebruikt zonder de vlag `--admin` . (Dit is het enige doel van 'Azure Kubernetes-gebruikersrol'.) Het resultaat, in een cluster met Azure AD-ondersteuning, is het downloaden van een lege vermelding [in](control-kubeconfig-access.md) , waarmee verificatie op basis van een browser wordt activeert wanneer deze voor het eerst wordt `.kube/config` gebruikt door `kubectl` .| De gebruiker maakt geen deel uit van deze groepen. Omdat de gebruiker geen clusterbeheerdersgroepen heeft, worden de rechten volledig beheerd door rolebindings of ClusterRoleBindings die zijn ingesteld door clusterbeheerders. De (Cluster)RoleBindings [benoemen Azure AD-gebruikers of Azure AD-groepen](azure-ad-rbac.md) als hun `subjects` . Als dergelijke bindingen niet zijn ingesteld, kan de gebruiker geen opdrachten `kubectl` uitvoeren.|Als u fijner toegangsbeheer wilt en u azure RBAC niet gebruikt voor Kubernetes-autorisatie. Houd er rekening mee dat de gebruiker die de bindingen in stelt, zich moet aanmelden met een van de andere methoden die in deze tabel worden vermeld.|
| Azure AD per lid van de beheergroep| Hetzelfde als hierboven|De gebruiker is lid van een van de groepen die hier worden vermeld. AKS genereert automatisch een ClusterRoleBinding die alle vermelde groepen verbindt met de `cluster-admin` Kubernetes-rol. Gebruikers in deze groepen kunnen dus alle opdrachten `kubectl` uitvoeren als `cluster-admin` .|Als u gebruikers eenvoudig volledige beheerdersrechten wilt verlenen en _geen_ Azure RBAC gebruikt voor Kubernetes-autorisatie.|
| Azure AD met Azure RBAC voor Kubernetes-autorisatie|Twee rollen: <br> Eerst azure **Kubernetes-gebruikersrol** (zoals hierboven). <br> Ten tweede, een van de 'Azure Kubernetes Service **RBAC...'** de hierboven vermelde rollen of uw eigen aangepaste alternatief.|Het veld Beheerdersrollen op het tabblad Configuratie is niet relevant wanneer Azure RBAC voor Kubernetes-autorisatie is ingeschakeld.|U gebruikt Azure RBAC voor Kubernetes-autorisatie. Deze aanpak biedt u een fijnkeurig beheer, zonder dat u RoleBindings of ClusterRoleBindings hoeft in te stellen.|

## <a name="next-steps"></a>Volgende stappen

- Zie Integrate Azure Active Directory with AKS (Integratie van azure AD en Kubernetes RBAC met AKS) om aan de slag te gaan met Azure AD en Kubernetes [RBAC.][aks-aad]
- Zie Best practices voor verificatie en [autorisatie in AKS][operator-best-practices-identity]voor de bijbehorende best practices.
- Zie Azure RBAC gebruiken om toegang te autorisatie binnen het [AKS-cluster (Azure Kubernetes Service)](manage-azure-rbac.md)om aan de slag te gaan met Azure RBAC voor Kubernetes-autorisatie.
- Zie Toegang tot het clusterconfiguratiebestand beperken om aan de slag te gaan met het beveiligen `kubeconfig` [van uw bestand](control-kubeconfig-access.md)

Zie de volgende artikelen voor meer informatie over kubernetes- en AKS-kernconcepten:

- [Kubernetes-/AKS-clusters en -workloads][aks-concepts-clusters-workloads]
- [Kubernetes-/AKS-beveiliging][aks-concepts-security]
- [Virtuele Kubernetes-/AKS-netwerken][aks-concepts-network]
- [Kubernetes/AKS-opslag][aks-concepts-storage]
- [Kubernetes/AKS-schaal][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
