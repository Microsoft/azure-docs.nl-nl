---
title: Toegang tot Azure-bestands shares beheren-on-premises AD DS-verificatie
description: Meer informatie over het toewijzen van machtigingen aan een Active Directory Domain Services identiteit die uw opslag account vertegenwoordigt. Zo kunt u de toegang beheren met verificatie op basis van een identiteit.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91716034"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Deel twee: machtigingen op share niveau toewijzen aan een identiteit

Voordat u aan dit artikel begint, moet u ervoor zorgen dat u het vorige artikel hebt voltooid, [AD DS verificatie inschakelen voor uw account](storage-files-identity-ad-ds-enable.md).

Zodra u Active Directory Domain Services (AD DS)-verificatie hebt ingeschakeld voor uw opslag account, moet u machtigingen op share niveau configureren om toegang te krijgen tot uw bestands shares. De identiteit die u wilt gebruiken voor toegang tot Azure file share-resources met moet een hybride identiteit zijn in zowel AD DS als Azure AD. Stel dat u een gebruiker hebt in uw AD DS dat is user1@onprem.contoso.com en u hebt gesynchroniseerd met Azure AD user1@contoso.com met behulp van Azure AD Connect-synchronisatie. Als u deze gebruiker toegang wilt geven Azure Files, moet u de machtigingen op share niveau toewijzen aan user1@contoso.com . Hetzelfde concept geldt voor groepen of service-principals. Daarom moet u de gebruikers en groepen van uw AD DS synchroniseren met Azure AD met behulp van Azure AD Connect-synchronisatie. 

Machtigingen op share niveau moeten worden toegewezen aan de Azure AD-identiteit die overeenkomt met dezelfde gebruiker of groep in uw AD DS om AD DS-verificatie te ondersteunen voor uw Azure-bestands share. Verificatie en autorisatie voor identiteiten die alleen bestaan in azure AD, zoals Azure Managed Identities (MSIs), worden niet ondersteund met AD DS-verificatie. In dit artikel wordt beschreven hoe u machtigingen op share niveau voor een bestands share toewijst aan een identiteit.


## <a name="share-level-permissions"></a>Machtigingen op share niveau

Over het algemeen kunt u het beste machtigingen op share niveau gebruiken voor toegangs beheer op hoog niveau voor een Azure AD-groep die een groep gebruikers en identiteiten vertegenwoordigt en vervolgens Windows-Acl's gebruikt voor gedetailleerd toegangs beheer tot het Directory-of bestands niveau. 

Er zijn drie ingebouwde rollen voor Azure voor het verlenen van machtigingen op share niveau voor gebruikers:

- Met de **SMB-share lezer van het opslag bestand** wordt lees toegang tot Azure Storage bestands shares via SMB toegestaan.
- **Gegevens van het opslag bestand SMB delen Inzender** maakt lees-, schrijf-en verwijder toegang mogelijk in azure Storage bestands shares via SMB.
- **Opslag bestands gegevens SMB-share met verhoogde bevoegdheid** is het toestaan van Windows-acl's voor lezen, schrijven, verwijderen en wijzigen in azure Storage bestands shares via SMB.

> [!IMPORTANT]
> Volledig beheer van een bestands share, inclusief de mogelijkheid om eigenaar te worden van een bestand, vereist het gebruik van de sleutel van het opslag account. Beheer beheer wordt niet ondersteund met Azure AD-referenties.

U kunt de Azure Portal, Azure PowerShell of de Azure CLI gebruiken om de ingebouwde rollen toe te wijzen aan de Azure AD-identiteit van een gebruiker voor het verlenen van machtigingen op share niveau.

## <a name="assign-an-azure-role"></a>Een Azure-rol toewijzen

### <a name="azure-portal"></a>Azure Portal

Voer de volgende stappen uit om een Azure-rol toe te wijzen aan een Azure AD-identiteit met behulp van de [Azure Portal](https://portal.azure.com):

1. Ga in het Azure Portal naar de bestands share of [Maak een bestands share](storage-how-to-create-file-share.md).
1. Selecteer **Access Control (IAM)**.
1. Selecteer **een roltoewijzing toevoegen**
1. Selecteer in de Blade **roltoewijzing toevoegen** de geschikte ingebouwde rol (opslag BESTANDS gegevens SMB-share, opslag BESTANDS gegevens SMB delen Inzender) uit de lijst met **functies** . Zorg ervoor dat er geen **toegang wordt toegewezen aan** de standaard instelling: **Azure AD-gebruiker,-groep of Service-Principal**. Selecteer de Azure AD-doel-id op naam of e-mail adres. **De geselecteerde Azure AD-identiteit moet een hybride identiteit zijn en mag niet alleen een Cloud identiteit zijn.** Dit betekent dat dezelfde identiteit ook wordt weer gegeven in AD DS.
1. Selecteer **Opslaan** om de roltoewijzings bewerking te volt ooien.

### <a name="powershell"></a>PowerShell

In het volgende Power shell-voor beeld ziet u hoe u een Azure-rol toewijst aan een Azure AD-identiteit, op basis van aanmeldings naam. Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de module Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)voor meer informatie over het toewijzen van Azure-rollen met Power shell.

Voordat u het volgende voorbeeld script uitvoert, vervangt u de waarden van de tijdelijke aanduidingen, inclusief haken, door uw waarden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
De volgende CLI 2,0-opdracht wijst een Azure-rol toe aan een Azure AD-identiteit, op basis van aanmeldings naam. Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure cli](../../role-based-access-control/role-assignments-cli.md)voor meer informatie over het toewijzen van Azure-rollen met Azure cli. 

Voordat u het volgende voorbeeld script uitvoert, moet u de waarden van de tijdelijke aanduidingen vervangen door uw eigen waarden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>Volgende stappen

Nu u machtigingen op share niveau hebt toegewezen, moet u machtigingen voor mappen en bestands niveau configureren. Ga door naar het volgende artikel.

[Deel drie: machtigingen voor mappen en bestands niveau configureren via SMB](storage-files-identity-ad-ds-configure-permissions.md)
