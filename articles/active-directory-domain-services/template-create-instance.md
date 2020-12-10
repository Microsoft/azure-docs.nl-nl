---
title: Azure DS Domain Services inschakelen met behulp van een sjabloon | Microsoft Docs
description: Leer hoe u Azure Active Directory Domain Services configureert en inschakelt met behulp van een Azure Resource Manager-sjabloon
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e18825da64d0d200f55ce72985ac843b93b1e612
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618787"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Een beheerd domein maken in Azure Active Directory Domain Services met behulp van een Azure Resource Manager-sjabloon

Azure AD DS (Active Directory Domain Services) biedt beheerde domeinservices - zoals domeindeelname, groepsbeleid, LDAP, Kerberos/NTLM-verificatie - die volledig compatibel zijn met Windows Server Active Directory. U gebruikt deze domeinservices zonder zelf domeincontrollers te implementeren, te beheren en er patches op toe te passen. Azure AD DS kan met uw bestaande Azure AD-tenant worden geïntegreerd. Met deze integratie kunnen gebruikers zich aanmelden met behulp van hun bedrijfsreferenties, en u kunt bestaande groepen en gebruikersaccounts gebruiken om de toegang tot resources te beveiligen.

Dit artikel laat zien hoe u een beheerd domain maakt met behulp van een Azure Resource Manager-sjabloon. Ondersteunende resources worden gemaakt met Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om dit artikel te voltooien:

* Installeer en configureer Azure PowerShell.
    * Indien nodig, volgt u de instructies voor [installeren van de Azure PowerShell-module en verbinding maken met uw Azure-abonnement](/powershell/azure/install-az-ps).
    * Zorg ervoor dat u zich bij uw Azure-abonnement aanmeldt met behulp van de [Connect-AzAccount][Connect-AzAccount]-cmdlet.
* Installeer en configureer Azure AD PowerShell.
    * Indien nodig, volgt u de instructies voor het [installeren van de Azure AD PowerShell-module en verbinding maken met Azure AD](/powershell/azure/active-directory/install-adv2).
    * Zorg ervoor dat u zich bij de Azure-tenant aanmeldt met behulp van de [Connect-AzureAD][Connect-AzureAD]-cmdlet.
* U hebt *bevoegdheden van een globale beheerder* voor de Azure AD-tenant nodig om Azure AD DS in te schakelen.
* U hebt bevoegdheden van een *Inzender* voor uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.

## <a name="dns-naming-requirements"></a>DNS-naamgevingsvereisten

Wanneer u een beheerd Azure AD DS-domein wilt maken, geeft u een DNS-naam op. Er is een aantal factoren waar u rekening mee moet houden wanneer u deze DNS-naam kiest:

* **Ingebouwde domeinnaam:** Standaard wordt de ingebouwde domeinnaam van de directory gebruikt (met het achtervoegsel *.onmicrosoft.com*). Als u beveiligde LDAP-toegang tot het beheerde domein wilt krijgen via een internetverbinding, kunt u geen digitaal certificaat maken om de verbinding met dit standaarddomein te beveiligen. Microsoft is de eigenaar van het domein *.onmicrosoft.com*, dus een Certificate Authority (CA) zal geen certificaat uitgeven.
* **Aangepaste domeinnamen:** De meestgebruikte methode is via het opgeven van een aangepaste domeinnaam, vaak een domein waarvan u al de eigenaar bent en waarnaar kan worden gerouteerd. Wanneer u een routeerbaar, aangepast domein gebruikt, kan verkeer op de juiste manier stromen om uw toepassingen te ondersteunen.
* **Niet-routeerbare domeinachtervoegsels:** Over het algemeen wordt aangeraden een niet-routeerbaar domeinnaamachtervoegsel, zoals *contoso.local*, te vermijden. Het achtervoegsel *.local* is niet routeerbaar en kan problemen veroorzaken met de DNS-resolutie.

> [!TIP]
> Als u een aangepaste domeinnaam maakt, wees dan voorzichtig met bestaande DNS-naamruimten. U wordt aangeraden een domeinnaam te gebruiken die losstaat van een bestaande Azure- of on-premises DNS-naamruimte.
>
> Als u bijvoorbeeld *contoso.com* als bestaande DNS-naamruimte hebt, maakt u een beheerd domein met de aangepaste domeinnaam *aaddscontoso.com*. Als u beveiligde LDAP moet gebruiken, moet u dit aangepaste domeinnaam registreren en hier eigenaar van zijn om de vereiste certificaten te genereren.
>
> Mogelijk moet u een aantal aanvullende DNS-records maken voor andere services in uw omgeving, of voorwaardelijke DNS-doorstuurservers maken tussen bestaande DNS-naamruimten in uw omgeving. Als u bijvoorbeeld een webserver hebt waar een site wordt gehost met de root-DNS-naam, kunnen naamconflicten ontstaan waardoor extra DNS-vermeldingen vereist zijn.
>
> In dit voorbeeld en deze artikelen met instructies wordt het aangepaste domein *aaddscontoso.com* gebruikt als een kort voorbeeld. In alle opdrachten geeft u uw eigen domeinnaam op.

De volgende DNS-naambeperkingen zijn ook van toepassing:

* **Beperkingen voor voorvoegsels voor domeinen:** U kunt geen beheerd domein maken met een voorvoegsel van meer dan 15 tekens. Het voorvoegsel van uw opgegeven domeinnaam (zoals *aaddscontoso* in de domeinnaam *aaddscontoso.com*) mag maximaal 15 tekens bevatten.
* **Conflicten met de netwerknaam:** De DNS-domeinnaam voor uw beheerde domein mag nog niet bestaan in het virtuele netwerk. Controleer met name op de volgende scenario's die kunnen leiden tot een naamconflict:
    * Of u al een Active Directory-domein met deze DNS-domeinnaam in het virtuele Azure-netwerk hebt.
    * Of het virtuele netwerk waarin u het beheerde domein wilt inschakelen, over een VPN-verbinding met uw on-premises netwerk beschikt. In dit scenario moet u ervoor zorgen dat u geen domein met dezelfde DNS-domeinnaam in uw on-premises netwerk hebt.
    * Als u het virtuele Azure-netwerk een bestaande Azure-cloudservice met deze naam bevat.

## <a name="create-required-azure-ad-resources"></a>Vereiste Azure AD-resources maken

Voor Azure AD DS is een service-principal en een Azure AD-groep vereist. Met deze resources kunnen via het beheerde domein gegevens worden gesynchroniseerd, en worden gedefinieerd welke gebruikers beheerdersbevoegdheden hebben in het beheerde domein.

Registreer eerst de Azure AD Domain Services-resourceprovider met behulp van de [Register-AzResourceProvider][Register-AzResourceProvider]-cmdlet:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Maak met behulp van de cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] een service-principal in Azure AD voor Azure AD DS voor communicatie en zelf-verificatie. Er wordt een specifieke toepassings-id gebruikt met de naam *Domeincontrollerservices* met de id *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Wijzig deze toepassings-id niet.

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

Maak nu met de cmdlet [New-AzureADGroup][New-AzureADGroup] een Azure AD-groep met de naam *AAD DC Administrators*. Aan gebruikers die zijn toegevoegd aan deze groep, worden vervolgens machtigingen verleend voor het uitvoeren van beheertaken in het beheerde domein.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Nu de groep *AAD DC-beheerders* is gemaakt, voegt u een gebruiker toe aan de groep met behulp van de cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember]. U haalt eerst de object-id voor de groep *AAD DC Administrators* op met behulp van de [Get-AzureADGroup][Get-AzureADGroup]-cmdlet. Vervolgens haalt u de object-id van de gewenste gebruiker op met behulp van de [Get-AzureADUser][Get-AzureADUser]-cmdlet.

In het volgende voorbeeld ziet u de gebruikersobject-id voor het account met een UPN van `admin@contoso.onmicrosoft.com`. Vervang dit gebruikersaccount door de UPN van de gebruiker die u wilt toevoegen aan de groep *AAD DC Administrators*:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Maak ten slotte een resourcegroep met behulp van de cdmlet [New-AzResourceGroup][New-AzResourceGroup]. De resourcegroep in het volgende voorbeeld heet *myResourceGroup* en is gemaakt in de regio *westus*. Gebruik uw eigen naam en gewenste regio:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Als u een regio kiest die beschikbaarheidszones ondersteunt, worden de Azure AD DS-resources gedistribueerd over zones voor extra redundantie. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's.

U hoeft niets te configureren voor Azure AD DS om te worden gedistribueerd over zones. De distributie van resources over zones wordt automatisch afgehandeld op het Azure-platform. Zie [Wat zijn beschikbaarheidszones in Azure?][availability-zones] voor meer informatie en om de beschikbaarheid van regio’s te zien.

## <a name="resource-definition-for-azure-ad-ds"></a>Resourcedefinitie voor Azure AD DS

Als onderdeel van de Resource Manager-resourcedefinitie zijn de volgende configuratieparameters vereist:

| Parameter               | Waarde |
|-------------------------|---------|
| domainName              | De DNS-domeinnaam voor uw beheerde domein, waarbij u rekening houdt met de vorige punten over de namen van prefixen en conflicten. |
| filteredSync            | In Azure AD DS kunt u *alle* gebruikers en groepen synchroniseren die beschikbaar zijn in Azure AD, of een synchronisatie *met een bereik* van alleen specifieke groepen.<br /><br /> Zie [Synchronisatie met een bereik in Azure AD Domain Services][scoped-sync] voor meer informatie over synchronisatie met een bereik.|
| notificationSettings    | Als er waarschuwingen worden gegenereerd in het beheerde domein, kunnen e-mailmeldingen worden verzonden. <br /><br />*Globale beheerders* van de Azure-tenant en leden van de groep *AAD DC Administrators* kunnen worden *Ingeschakeld* voor deze meldingen.<br /><br /> Indien gewenst kunt u aanvullende ontvangers voor meldingen toevoegen wanneer er meldingen zijn die aandacht vereisen.|
| domainConfigurationType | Standaard wordt een beheerd domein gemaakt als een *gebruiker* sforest. Met dit type forest worden alle objecten van Azure AD gesynchroniseerd, waaronder alle gebruikersaccounts die zijn gemaakt in een on-premises AD DS-omgeving. U hoeft geen *domainConfiguration*-waarde op te geven om een gebruikersforest te maken.<br /><br /> Met een *resource* forest worden alleen gebruikers en groepen gesynchroniseerd die rechtstreeks in Azure AD zijn gemaakt. Stel de waarde in op *ResourceTrusting* om een resourceforest te maken.<br /><br />Voor meer informatie over *resource* forests, zoals redenen om deze te gebruiken en hoe u foresttrusts maakt met on-premises AD DS-domeinen, raadpleegt u [Overzicht van Azure AD DS-resourceforests][resource-forests].|

De volgende verkorte parameterdefinitie laat zien hoe deze waarden worden gedeclareerd. Er wordt een gebruikersforest met de naam *aaddscontoso.com* gemaakt met alle gebruikers van Azure AD die worden gesynchroniseerd met het beheerde domein:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Vervolgens wordt het volgende verkorte Resource Manager-sjabloonbrontype gebruikt om het beheerde domein te definiëren en te maken. Er moeten al een virtueel netwerk van Azure en een subnet bestaan, of als onderdeel van de Resource Manager-sjabloon worden gemaakt. Het beheerde domein wordt verbonden met dit subnet.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Deze parameters en dit resourcetype kunnen worden gebruikt als onderdeel van een bredere Resource Manager-sjabloon voor het implementeren van een beheerd domein, zoals wordt weergegeven in de volgende sectie.

## <a name="create-a-managed-domain-using-sample-template"></a>Een beheerd domein maken met behulp van een voorbeeldsjabloon

Met de volgende volledige Resource Manager-voorbeeldsjabloon worden een beheerd domein en de ondersteunende virtuele netwerk-, subnet- en netwerkbeveiligingsgroepregels gemaakt. De netwerkbeveiligingsgroepregels zijn nodig om het beheerde domein te beveiligen en ervoor te zorgen dat het verkeer correct kan verlopen. Er wordt een gebruikersforest met de DNS-naam *aaddscontoso.com* gemaakt, met alle vanuit Azure AD gesynchroniseerde gebruikers:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Deze sjabloon kan worden geïmplementeerd met uw favoriete implementatiemethode, zoals de [Azure-portal][portal-deploy], [Azure PowerShell][powershell-deploy]of een CI/CD-pijplijn. In het volgende voorbeeld wordt de [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment]-cmdlet gebruikt. Geef de naam van uw eigen resourcegroep en de bestandsnaam van de sjabloon op:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Het duurt enkele minuten om de resource te maken en het beheer terug te geven aan de PowerShell-prompt. Het inrichten van het beheerde domein wordt op de achtergrond voortgezet. Het kan een uur duren voordat de implementatie is voltooid. Op de **Overzichtspagina** voor uw beheerde domein in de Azure-portal wordt de huidige status weergegeven tijdens deze implementatiefase.

Wanneer in de Azure-portal wordt weergegeven dat inrichten van het beheerde domein gereed is, moeten de volgende taken worden voltooid:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat virtuele machines het beheerde domein kunnen vinden voor domeindeelname of verificatie.
    * Selecteer het beheerde domein in de portal om DNS te configureren. In het **Overzichtsvenster** wordt u gevraagd om deze DNS-instellingen automatisch te configureren.
* U moet [Wachtwoordsynchronisatie in Azure AD DS inschakelen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich bij het beheerde domein kunnen aanmelden met hun bedrijfsreferenties.

## <a name="next-steps"></a>Volgende stappen

Als u het beheerde domein in actie wilt zien, kunt u [een Windows-VM koppelen][windows-join], [beveiligde LDAP configureren][tutorial-ldaps] en [wachtwoordhashsynchronisatie configureren][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment