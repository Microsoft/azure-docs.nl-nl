---
title: Beheerde identiteiten beveiligen in Azure Active Directory
description: Uitleg over het zoeken, evalueren en verbeteren van de beveiliging van beheerde identiteiten.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88a7600239d6e960fa2e635c9e7d9049a7c02db3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032349"
---
# <a name="securing-managed-identities"></a>Beheerde identiteiten beveiligen

Ontwikkel aars worden vaak aangevraagd door het beheer van geheimen en referenties die worden gebruikt voor het beveiligen van communicatie tussen verschillende services. Beheerde identiteiten zijn beveiligde Azure Active Directory-identiteiten (Azure AD) die zijn gemaakt om identiteiten voor Azure-resources te bieden.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Voor delen van het gebruik van beheerde identiteiten voor Azure-resources

Hier volgen enkele voor delen van het gebruik van beheerde identiteiten:

* U hoeft geen referenties te beheren. Met beheerde identiteiten worden referenties volledig beheerd, gedraaid en beveiligd door Azure. Identiteiten worden automatisch gegeven en verwijderd met Azure-resources. Met beheerde identiteiten kunnen Azure-resources communiceren met alle services die ondersteuning bieden voor Azure AD-verificatie.

* Niemand (inclusief een globale beheerder) heeft toegang tot de referenties, zodat deze niet per ongeluk kan worden gelekt door bijvoorbeeld te worden opgenomen in code.

## <a name="when-to-use-managed-identities"></a>Wanneer moet u beheerde identiteiten gebruiken?

Beheerde identiteiten worden het beste gebruikt voor communicatie tussen services die ondersteuning bieden voor Azure AD-verificatie. 

Een bron systeem vraagt toegang tot een doel service. Elke Azure-resource kan een bron systeem zijn. Een voor beeld: een exemplaar van een Azure-VM, een Azure-functie en een Azure-app-service ondersteunt beheerde identiteiten.

[!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>Hoe verificatie en autorisatie werkt

Met beheerde identiteiten kan het bron systeem een token van Azure AD verkrijgen zonder dat de bron eigenaar referenties hoeft te beheren. Azure beheert de referenties. Het token dat is verkregen door het bron systeem wordt weer gegeven voor verificatie op het doel systeem. 

Het doel systeem moet het bron systeem verifiëren (identificeren) en autoriseren voordat u toegang toestaat. Wanneer de doel service Azure AD-gebaseerde verificatie ondersteunt, wordt een toegangs token geaccepteerd dat is uitgegeven door Azure AD. 

Azure heeft een besturings vlak en een gegevens vlak. In het vlak van het besturings element maakt u resources en op het gegevens vlak dat u opent. U kunt bijvoorbeeld een Cosmos-data base maken in het besturings vlak, maar deze in het gegevens vlak opvragen.

Zodra het doel systeem het token voor authenticatie heeft geaccepteerd, kunnen er verschillende mechanismen voor autorisatie worden ondersteund voor het besturings vlak en het gegevens vlak van de toepassing.

Alle bewerkingen van het beheer vlak van Azure worden beheerd door [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) en maken gebruik [van Azure Role Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview). In het vlak van gegevens heeft elk doel systeem een eigen autorisatie mechanisme. Azure Storage ondersteunt Azure RBAC op het vlak van de gegevens. Toepassingen die gebruikmaken van Azure-app Services kunnen bijvoorbeeld gegevens lezen van Azure Storage en toepassingen die gebruikmaken van de Azure Kubernetes-service kunnen geheimen lezen die zijn opgeslagen in Azure Key Vault.

Zie voor meer informatie over besturings-en gegevens abonnementen [Control vlak en data vlak Operations-Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/control-plane-and-data-plane).

Alle Azure-Services ondersteunen uiteindelijk beheerde identiteiten. Zie [Services die beheerde identiteiten voor Azure-resources ondersteunen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)voor meer informatie.

##  

## <a name="types-of-managed-identities"></a>Typen beheerde identiteiten

Er zijn twee soorten beheerde identiteiten: systeem toegewezen en door de gebruiker toegewezen.

Door het systeem toegewezen beheerde identiteit heeft de volgende eigenschappen:

* Ze hebben 1:1-relatie met de Azure-resource. Er is bijvoorbeeld een unieke beheerde identiteit gekoppeld aan elke VM.

* Ze zijn gekoppeld aan de levens cyclus van Azure-resources. Wanneer de resource wordt verwijderd, wordt de beheerde identiteit die eraan is gekoppeld, automatisch verwijderd, waardoor het risico dat is gekoppeld aan zwevende accounts, wordt geëlimineerd. 

Door de gebruiker toegewezen beheerde identiteiten hebben de volgende eigenschappen:

* De levens cyclus van deze identiteiten is onafhankelijk van een Azure-resource en u moet de levens cyclus beheren. Wanneer de Azure-resource wordt verwijderd, wordt de toegewezen beheerde identiteit die aan de gebruiker is toegewezen, niet automatisch voor u verwijderd.

* Een door de gebruiker toegewezen beheerde identiteit kan worden toegewezen aan nul of meer Azure-resources.

* Ze kunnen vooraf van de tijd worden gemaakt en vervolgens aan een resource worden toegewezen.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Beheerde ID-service-principals zoeken in azure AD

Er zijn verschillende manieren waarop u beheerde identiteiten kunt vinden:

* Gebruik de pagina bedrijfs toepassingen in de Azure Portal

* Microsoft Graph gebruiken

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

1. Selecteer bedrijfs toepassing in azure AD.

2. Het filter voor beheerde identiteiten selecteren 

   ![Afbeelding van het scherm alle toepassingen met de vervolg keuzelijst voor het toepassings type ' beheerde identiteiten markeren '.](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Microsoft Graph gebruiken

U kunt een lijst met alle beheerde identiteiten in uw Tenant ophalen met de volgende GET-aanvraag voor Microsoft Graph:

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

U kunt deze aanvragen filteren. Zie de documentatie van Graph voor [Get servicePrincipal](/graph/api/serviceprincipal-get?view=)voor meer informatie.

## <a name="assess-the-security-of-managed-identities"></a>De beveiliging van beheerde identiteiten evalueren 

U kunt de beveiliging van beheerde identiteiten op de volgende manieren beoordelen:

* Controleer de bevoegdheden en zorg ervoor dat het model voor de minste privilegeing is geselecteerd. Gebruik de volgende Power shell-cmdlet om de machtigingen op te halen die aan uw beheerde identiteiten zijn toegewezen.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Zorg ervoor dat de beheerde identiteit geen deel uitmaakt van geprivilegieerde groepen, zoals een groep Administrators.  
U kunt dit doen door de leden van uw groepen met hoge bevoegdheden te inventariseren met Power shell.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Zorg ervoor dat u weet in welke bronnen de beheerde identiteit wordt geopend](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell).

## <a name="move-to-managed-identities"></a>Verplaatsen naar beheerde identiteiten

Als u een service-principal of een Azure AD-gebruikers account gebruikt, evalueer dan of u in plaats daarvan een beheerd kunt gebruiken om te voor komen dat referenties moeten worden beveiligd, gedraaid en beheerd. 

## <a name="next-steps"></a>Volgende stappen

**Zie voor meer informatie over het maken van beheerde identiteiten:** 

[Maak een door de gebruiker toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal). 

[Een door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

[Door het systeem toegewezen beheerde identiteit inschakelen voor een bestaande resource](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Zie voor meer informatie over service accounts:**

[Inleiding tot Azure Active Directory-service accounts](service-accounts-introduction-azure.md)

[Service-principals beveiligen](service-accounts-principal.md)

[Azure-service accounts beheren](service-accounts-governing-azure.md)

[Inleiding tot on-premises service accounts](service-accounts-on-premises.md)

 

 

 
