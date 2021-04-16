---
title: Een klant onboarden in Azure Lighthouse
description: Leer hoe u een klant onboardt voor Azure Lighthouse, zodat hun resources toegankelijk zijn en kunnen worden beheerd via uw eigen tenant met behulp van gedelegeerd Azure-resourcebeheer.
ms.date: 03/29/2021
ms.topic: how-to
ms.openlocfilehash: d8ad448ac022b07ecdea6b68c4544b8c955814b1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497962"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Een klant onboarden in Azure Lighthouse

In dit artikel wordt uitgelegd hoe u als serviceprovider een klant kunt onboarden voor Azure Lighthouse. Wanneer u dit doet, kunnen gedelegeerde resources (abonnementen en/of resourcegroepen) in de Azure Active Directory-tenant (Azure AD) van de klant worden beheerd via uw eigen tenant met behulp van [Gedelegeerd resourcebeheer](../concepts/azure-delegated-resource-management.md)van Azure.

> [!TIP]
> Hoewel we in dit onderwerp naar serviceproviders en klanten verwijzen, kunnen ondernemingen die meerdere [tenants](../concepts/enterprise.md) beheren, hetzelfde proces gebruiken voor het instellen van Azure Lighthouse en consolideren van hun beheerervaring.

U kunt het onboardingproces herhalen voor meerdere klanten. Wanneer een gebruiker met de juiste machtigingen zich bij uw beherende tenant meldt, kan die gebruiker worden geautoriseerd binnen klanttenancybereiken om beheerbewerkingen uit te voeren, zonder dat deze zich bij elke afzonderlijke klantten tenant moet aanmelden.

Als u uw impact op klantbetrokkenheid wilt bijhouden en herkenning wilt ontvangen, koppelt u uw MICROSOFT PARTNER NETWORK-id (MPN) aan ten minste één gebruikersaccount dat toegang heeft tot elk van uw onboarding-abonnementen. U moet deze associatie uitvoeren in de tenant van uw serviceprovider. We raden u aan een service-principalaccount te maken in uw tenant dat is gekoppeld aan uw MPN-id, en deze service-principal vervolgens elke keer dat u onboarding voor een klant maakt, op te nemen. Zie Uw partner-id koppelen om partnertegoed in te stellen [voor gedelegeerde resources voor meer informatie.](partner-earned-credit.md)

> [!NOTE]
> Klanten kunnen ook onboarding voor Azure Lighthouse wanneer ze een Managed Service-aanbieding (openbaar of privé) kopen die u publiceert [naar Azure Marketplace](publish-managed-services-offers.md). U kunt ook het onboardingproces gebruiken dat hier wordt beschreven, samen met aanbiedingen die zijn gepubliceerd Azure Marketplace.

Voor het onboardingproces moeten acties worden ondernomen vanuit zowel de tenant van de serviceprovider als de tenant van de klant. Al deze stappen worden beschreven in dit artikel.

## <a name="gather-tenant-and-subscription-details"></a>Tenant- en abonnementsgegevens verzamelen

Als u de tenant van een klant wilt onboarden, moet deze een actief Azure-abonnement hebben. U moet het volgende weten:

- De tenant-id van de tenant van de serviceprovider (waar u de resources van de klant gaat beheren)
- De tenant-id van de tenant van de klant (met resources die worden beheerd door de serviceprovider)
- De abonnements-ID's voor elk specifiek abonnement in de tenant van de klant die worden beheerd door de serviceprovider (of die de resourcegroep(en) bevat die worden beheerd door de serviceprovider.

Als u deze id-waarden nog niet hebt, kunt u ze op een van de volgende manieren ophalen. Zorg ervoor dat u deze exacte waarden gebruikt in uw implementatie.

### <a name="azure-portal"></a>Azure Portal

U kunt uw tenant-id zien door de muisaanwijzer over uw accountnaam te bewegen in de rechterbovenhoek van de Azure Portal of door Schakelen tussen **mappen te selecteren.** Als u uw tenant-id wilt selecteren en kopiëren, zoekt u  Azure Active Directory in de portal, selecteert u Eigenschappen en kopieert u de waarde die wordt weergegeven in het veld **Map-id.** Als u een abonnements-id wilt vinden in de tenant van de klant, gaat u naar Abonnementen en selecteert u vervolgens de juiste abonnements-id.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Bij het onboarden van een abonnement (of een of meer resourcegroepen binnen een abonnement) met behulp van het proces dat hier wordt beschreven, wordt de resourceprovider **Microsoft.ManagedServices** geregistreerd voor dat abonnement.

## <a name="define-roles-and-permissions"></a>Rollen en machtigingen definiëren

Als serviceprovider wilt u mogelijk meerdere taken uitvoeren voor één klant, waarvoor verschillende toegangsgegevens voor verschillende bereiken zijn vereist. U kunt zoveel autorisaties definiëren als u nodig hebt om de juiste ingebouwde [Azure-rollen toe te wijzen.](../../role-based-access-control/built-in-roles.md) Elke autorisatie bevat **een principalId** die verwijst naar een Azure AD-gebruiker, -groep of -service-principal in de beherende tenant.

> [!NOTE]
> Tenzij expliciet opgegeven, kunnen verwijzingen naar een 'gebruiker' in de Azure Lighthouse-documentatie van toepassing zijn op een Azure AD-gebruiker, -groep of -service-principal in een autorisatie.

Om het beheer gemakkelijker te maken, raden we u aan waar mogelijk Azure AD-gebruikersgroepen te gebruiken voor elke rol, in plaats van voor afzonderlijke gebruikers. Dit biedt u de flexibiliteit om afzonderlijke gebruikers toe te voegen aan of te verwijderen uit de groep die toegang heeft, zodat u het onboardingproces niet hoeft te herhalen om gebruikerswijzigingen aan te brengen. U kunt ook rollen toewijzen aan een service-principal, wat handig kan zijn voor automatiseringsscenario's.

> [!IMPORTANT]
> Als u machtigingen voor een Azure AD-groep wilt toevoegen, moet **het groepstype** worden ingesteld op **Beveiliging**. Deze optie wordt geselecteerd wanneer de groep wordt gemaakt. Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie.

Zorg er bij het definiëren van uw autorisaties voor dat u het principe van de minste bevoegdheden volgt, zodat gebruikers alleen de machtigingen hebben die nodig zijn om hun taak te voltooien. Zie [Tenants, gebruikers](../concepts/tenants-users-roles.md)en rollen in Azure Lighthouse scenario's voor informatie over ondersteunde rollen Azure Lighthouse best practices.

Als u autorisaties wilt definiëren, moet u de id-waarden weten voor elke gebruiker, gebruikersgroep of service-principal in de tenant van de serviceprovider waaraan u toegang wilt verlenen. U hebt ook de roldefinitie-id nodig voor elke ingebouwde rol die u wilt toewijzen. Als u ze nog niet hebt, kunt u ze ophalen door de onderstaande opdrachten uit te voeren vanuit de tenant van de serviceprovider.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> U wordt aangeraden de rol Registratietoewijzing voor beheerde [services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) toe te wijzen bij het onboarden van een klant, zodat gebruikers in uw tenant de toegang tot de overdracht [later,](remove-delegation.md) indien nodig, kunnen verwijderen. Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de tenant van de klant.

## <a name="create-an-azure-resource-manager-template"></a>Een sjabloon Azure Resource Manager maken

Voor het onboarden van uw klant moet u een [Azure Resource Manager](../../azure-resource-manager/index.yml)-sjabloon maken voor uw aanbieding, met de volgende gegevens. De **waarden mspOfferName** **en mspOfferDescription** zijn zichtbaar voor de klant op de [pagina Serviceproviders](view-manage-service-providers.md) van de Azure Portal.

|Veld  |Definitie  |
|---------|---------|
|**mspOfferName**     |Een naam die deze definitie beschrijft. Deze waarde wordt aan de klant weergegeven als de titel van de aanbieding en moet een unieke waarde zijn.        |
|**mspOfferDescription**     |Een korte beschrijving van uw aanbieding (bijvoorbeeld 'Contoso VM-beheeraanbieding').      |
|**managedByTenantId**     |Uw tenant-id.          |
|**Vergunningen**     |De **principalId-waarden** voor de gebruikers/groepen/SPN's van uw tenant, elk met een **principalIdDisplayName** om uw klant inzicht te geven in het doel van de autorisatie, en worden aan een ingebouwde **roleDefinitionId-waarde** toe te kennen om het toegangsniveau op te geven.      |

Voor het onboardingproces is een Azure Resource Manager sjabloon (beschikbaar in onze [voorbeeld-repo)](https://github.com/Azure/Azure-Lighthouse-samples/)en een bijbehorend parametersbestand dat u wijzigt zodat deze overeenkomt met uw configuratie en uw autorisaties definieert.

> [!IMPORTANT]
> Voor het proces dat hier wordt beschreven, is een afzonderlijke implementatie vereist voor elk abonnement dat wordt onboarding, zelfs als u abonnementen in dezelfde tenant van de klant onboardt. Er zijn ook afzonderlijke implementaties vereist als u meerdere resourcegroepen binnen verschillende abonnementen in dezelfde klantten tenant onboardt. Het onboarden van meerdere resourcegroepen binnen één abonnement kan echter in één implementatie worden uitgevoerd.
>
> Er zijn ook afzonderlijke implementaties vereist voor meerdere aanbiedingen die worden toegepast op hetzelfde abonnement (of resourcegroepen binnen een abonnement). Elke toegepaste aanbieding moet een andere **mspOfferName gebruiken.**

De sjabloon die u kiest, is afhankelijk van of u een volledig abonnement, een resourcegroep of meerdere resourcegroepen binnen een abonnement onboardt. We bieden ook een sjabloon die kan worden gebruikt voor klanten die een beheerde-serviceaanbieding hebben aangeschaft die u hebt gepubliceerd naar Azure Marketplace, als u liever op deze manier onboarding van hun abonnement(en) wilt maken.

|Onboarding van dit  |Gebruik deze Azure Resource Manager sjabloon  |En wijzig dit parameterbestand |
|---------|---------|---------|
|Abonnement   |[delegatedResourceManagement.jsaan](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.jsaan](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resourcegroep   |[rgDelegatedResourceManagement.jsaan](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.jsaan](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Meerdere resourcegroepen binnen een abonnement   |[multipleRgDelegatedResourceManagement.jsaan](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.jsaan](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Abonnement (wanneer u een aanbieding gebruikt die is gepubliceerd naar Azure Marketplace)   |[marketplaceDelegatedResourceManagement.jsaan](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.jsaan](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Hoewel u geen volledige beheergroep in één implementatie kunt onboarden, kunt u een beleid [implementeren op beheergroepsniveau.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) Het beleid maakt gebruik van het [effect deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) om te controleren of elk abonnement in de beheergroep is gedelegeerd aan de opgegeven beherende tenant. Zo niet, dan maakt de toewijzing op basis van de waarden die u op geeft. Vervolgens hebt u toegang tot alle abonnementen in de beheergroep, hoewel u er als afzonderlijke abonnementen aan moet werken (in plaats van acties uit te voeren op de beheergroep als geheel).

In het volgende voorbeeld ziet u een **delegatedResourceManagement.parameters.jseen bestand** dat kan worden gebruikt voor onboarding van een abonnement. De parameterbestanden van de resourcegroep (in de map [rg-delegated-resource-management)](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) zijn vergelijkbaar, maar bevatten ook een **parameter rgName** om de specifieke resourcegroep(s) te identificeren die moeten worden toegevoegd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

Met de laatste autorisatie in het bovenstaande voorbeeld wordt een **principalId** toegevoegd met de rol Gebruikerstoegangbeheerder (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Wanneer u deze rol toewijst, moet u **de eigenschap delegatedRoleDefinitionIds** en een of meer ondersteunde ingebouwde Azure-rollen opnemen. De gebruiker die in deze autorisatie is gemaakt, kan deze rollen toewijzen aan beheerde identiteiten [in](../../active-directory/managed-identities-azure-resources/overview.md) de tenant van de klant. Dit is vereist voor het implementeren van beleidsregels die kunnen [worden herstellen.](deploy-policy-remediation.md)  De gebruiker kan ook ondersteuningsincidenten maken. Andere machtigingen die normaal gesproken zijn gekoppeld aan de rol Administrator voor gebruikerstoegang, zijn niet van toepassing op **deze principalId.**

## <a name="deploy-the-azure-resource-manager-templates"></a>De sjablonen Azure Resource Manager implementeren

Nadat u het parameterbestand hebt bijgewerkt, moet een gebruiker in de tenant van de klant de sjabloon Azure Resource Manager in de tenant implementeren. Er is een afzonderlijke implementatie nodig voor elk abonnement dat u wilt onboarden (of voor elk abonnement dat resourcegroepen bevat die u wilt onboarden).

> [!IMPORTANT]
> Deze implementatie moet worden uitgevoerd door een niet-gastaccount in de tenant van de klant met een rol met de machtiging , zoals Eigenaar , voor het abonnement waarvoor onboarding wordt uitgevoerd (of dat de resourcegroepen bevat die worden `Microsoft.Authorization/roleAssignments/write` onboarding uitgevoerd). [](../../role-based-access-control/built-in-roles.md#owner) Om gebruikers te vinden die het abonnement kunnen delegeren, kan een gebruiker in de tenant van de klant het abonnement selecteren in de Azure Portal, Toegangsbeheer **(IAM)** openen en alle gebruikers met de rol Eigenaar [weergeven.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription) 
>
> Als het abonnement is gemaakt via [het Cloud Solution Provider -programma (CSP),](../concepts/cloud-solution-provider.md)kan elke gebruiker met de rol Beheerderagent in de tenant van uw serviceprovider de implementatie uitvoeren. [](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles)

De implementatie kan worden uitgevoerd in de Azure Portal, met behulp van PowerShell of met behulp van Azure CLI, zoals hieronder wordt weergegeven.

### <a name="azure-portal"></a>Azure Portal

1. Selecteer in [onze GitHub-opslagplaats](https://github.com/Azure/Azure-Lighthouse-samples/)de knop **Implementeren in Azure** die wordt weergegeven naast de sjabloon die u wilt gebruiken. De sjabloon wordt in Azure Portal geopend.
1. Voer uw waarden in voor **Msp-aanbiedingsnaam,** **Msp-aanbiedingsbeschrijving,** **Beheerd door tenant-id** en **Autorisaties.** Als u wilt, kunt u Parameters **bewerken selecteren om** waarden voor , , en rechtstreeks in het `mspOfferName` `mspOfferDescription` `managedbyTenantId` `authorizations` parameterbestand in te voeren. Zorg ervoor dat u deze waarden bij te werken in plaats van de standaardwaarden uit de sjabloon te gebruiken.
1. Selecteer **Controleren en maken** en selecteer vervolgens **Maken.**

Na enkele minuten ziet u een melding dat de implementatie is voltooid.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>Bevestigen dat onboarding is geslaagd

Wanneer de onboarding van een klantabonnement voor Azure Lighthouse is geslaagd, kunnen gebruikers in de tenant van de serviceprovider het abonnement en de resources ervan zien (als ze via het bovenstaande proces toegang tot het abonnement hebben gekregen, afzonderlijk of als lid van een Azure AD-groep met de juiste machtigingen). Controleer of het abonnement op een van de volgende manieren wordt weergegeven om dit te bevestigen.  

### <a name="azure-portal"></a>Azure Portal

In de tenant van de serviceprovider:

1. Navigeer naar [de pagina Mijn klanten.](view-manage-customers.md)
2. Selecteer **Klanten**.
3. Controleer of u de abonnementen kunt zien met de naam van de aanbieding die u hebt opgegeven in de Resource Manager sjabloon.

> [!IMPORTANT]
> Als u het gedelegeerde abonnement wilt zien [in](view-manage-customers.md)Mijn klanten, moeten gebruikers [](../../role-based-access-control/built-in-roles.md#reader) in de tenant van de serviceprovider de rol Lezer hebben (of een andere ingebouwde rol die lezertoegang omvat) wanneer het abonnement is onboarding heeft.

In de tenant van de klant:

1. Navigeer naar [de pagina Serviceproviders.](view-manage-service-providers.md)
2. Selecteer **Aanbiedingen van serviceproviders**.
3. Controleer of u de abonnementen kunt zien met de naam van de aanbieding die u hebt opgegeven in de Resource Manager sjabloon.

> [!NOTE]
> Het kan tot 15 minuten duren nadat uw implementatie is voltooid voordat de updates worden weergegeven in de Azure Portal. Mogelijk kunt u de updates eerder zien als u uw Azure Resource Manager-token bij werkt door de browser te vernieuwen, u aan te melden en af te melden of een nieuw token aan te vragen.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

Als u wijzigingen moet aanbrengen nadat de onboarding van de klant is doorgevoerd, kunt u [de delegering bijwerken.](update-delegation.md) U kunt ook [de toegang tot de overdracht volledig](remove-delegation.md) verwijderen.

## <a name="troubleshooting"></a>Problemen oplossen

Als de onboarding van uw klant niet lukt of als uw gebruikers problemen hebben met het openen van de gedelegeerde resources, controleert u de volgende tips en vereisten en probeert u het opnieuw.

- De `managedbyTenantId` waarde mag niet gelijk zijn aan de tenant-id voor het abonnement dat wordt onboarden.
- U kunt niet meerdere toewijzingen hebben in hetzelfde bereik met hetzelfde `mspOfferName` .
- De **resourceprovider Microsoft.ManagedServices** moet zijn geregistreerd voor het gedelegeerde abonnement. Dit zou automatisch moeten gebeuren tijdens de implementatie, maar als dat niet het geval is, kunt u [dit handmatig registreren.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)
- Autorisaties mogen geen gebruikers bevatten [met](../../role-based-access-control/built-in-roles.md#owner) de ingebouwde rol Eigenaar of ingebouwde rollen met [DataActions.](../../role-based-access-control/role-definitions.md#dataactions)
- Groepen moeten worden gemaakt met [**Groepstype**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) ingesteld op **Beveiliging** en niet **Microsoft 365**.
- Mogelijk is er een extra vertraging voordat toegang wordt ingeschakeld voor [geneste groepen.](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)
- Gebruikers die resources in de Azure Portal moeten beschikken over de rol [Lezer](../../role-based-access-control/built-in-roles.md#reader) (of een andere ingebouwde rol die lezertoegang omvat).
- De [ingebouwde Azure-rollen die](../../role-based-access-control/built-in-roles.md) u in autorisaties op te nemen, mogen geen afgeschafte rollen bevatten. Als een ingebouwde rol van Azure wordt afgeschaft, verliezen gebruikers die met deze rol zijn onboarding uitgevoerd, de toegang en kunt u geen extra delegaties meer onboarden. U kunt dit oplossen door uw sjabloon bij te werken, alleen ondersteunde ingebouwde rollen te gebruiken en vervolgens een nieuwe implementatie uit te voeren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerervaring in meerdere tenants](../concepts/cross-tenant-management-experience.md).
- [Bekijk en beheer klanten](view-manage-customers.md) door naar **Mijn klanten** in de Azure Portal.
- Meer informatie over het [bijwerken](update-delegation.md) of [verwijderen van](remove-delegation.md) een delegering.
