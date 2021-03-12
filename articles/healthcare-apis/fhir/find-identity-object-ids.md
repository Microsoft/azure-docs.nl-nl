---
title: "Id-object-Id's voor verificatie zoeken: Azure API voor FHIR"
description: In dit artikel wordt uitgelegd hoe u de id-object-Id's kunt vinden die nodig zijn om verificatie voor Azure API voor FHIR te configureren
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 706d7e081743f2bab1f593e00dc792f218a000ea
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018020"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Id-object-Id's zoeken voor verificatie configuratie

In dit artikel leert u hoe u identiteits object-Id's kunt vinden die nodig zijn bij het configureren van de Azure API voor FHIR om [een externe of secundaire Active Directory Tenant](configure-local-rbac.md) voor gegevens vlak te gebruiken.

## <a name="find-user-object-id"></a>Gebruikers object-ID zoeken

Als u een gebruiker met gebruikers naam hebt `myuser@contoso.com` , kunt u de gebruikers vinden `ObjectId` met behulp van de volgende Power shell-opdracht:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

u kunt ook de Azure CLI gebruiken:

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>De object-ID van de Service-Principal zoeken

Stel dat u een [Service-client-app](register-service-azure-ad-client-app.md) hebt geregistreerd en u wilt toestaan dat deze service-client toegang heeft tot de Azure API voor FHIR. u kunt de object-id voor de client Service-Principal vinden met de volgende Power shell-opdracht:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

waar `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` de client toepassings-id van de service is. U kunt ook de `DisplayName` Service-client gebruiken:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Als u de Azure CLI gebruikt, kunt u het volgende gebruiken:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>Een object-ID van een beveiligings groep zoeken

Als u de object-ID van een beveiligings groep wilt zoeken, kunt u de volgende Power shell-opdracht gebruiken:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Waar `mygroup` de naam is van de groep waarin u bent geïnteresseerd.

Als u de Azure CLI gebruikt, kunt u het volgende gebruiken:

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u identiteits object-Id's kunt vinden die nodig zijn om de Azure API voor FHIR te configureren voor het gebruik van een externe of secundaire Azure Active Directory Tenant. Lees de volgende informatie over het gebruik van de object-Id's voor het configureren van lokale RBAC-instellingen:
 
>[!div class="nextstepaction"]
>[Lokale RBAC-instellingen configureren](configure-local-rbac.md)
