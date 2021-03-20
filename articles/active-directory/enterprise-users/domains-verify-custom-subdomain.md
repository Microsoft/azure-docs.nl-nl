---
title: Type verificatie van subdomein wijzigen met behulp van Power shell en Graph-Azure Active Directory | Microsoft Docs
description: De standaard instellingen voor verificatie van subdomeinen die zijn overgenomen van de hoofd domein instellingen, worden gewijzigd in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734e6824f13e62ad080500eff18c4892e1f76807
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95503671"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Het verificatie type van het subdomein wijzigen in Azure Active Directory

Nadat een hoofd domein is toegevoegd aan Azure Active Directory (Azure AD), nemen alle volgende subdomeinen die aan die hoofdmap in uw Azure AD-organisatie zijn toegevoegd, automatisch de verificatie-instelling over van het hoofd domein. Als u echter de instellingen voor domein verificatie onafhankelijk van de hoofd domein instellingen wilt beheren, kunt u nu met de API voor Microsoft Graph. Als u bijvoorbeeld een federatief hoofd domein hebt, zoals contoso.com, kunt u dit artikel helpen bij het controleren van een subdomein, zoals child.contoso.com, in plaats van Federated.

Wanneer het bovenliggende domein in de Azure AD-Portal federatief is en de beheerder een beheerd subdomein probeert te verifiëren op de pagina **aangepaste domein namen** , krijgt u de fout ' kan het domein niet toevoegen ' met de reden "een of meer eigenschappen bevatten ongeldige waarden". Als u dit subdomein probeert toe te voegen vanuit het Microsoft 365-beheer centrum, wordt een vergelijk bare fout weer gegeven. Meer informatie over de fout vindt u [in een onderliggend domein wijzigingen in het bovenliggende domein niet overnemen in Office 365, Azure of intune](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Een aangepast subdomein controleren

Omdat subdomeinen het verificatie type van het hoofd domein standaard overnemen, moet u het subdomein promo veren naar een hoofd domein in azure AD met behulp van de Microsoft Graph zodat u het verificatie type kunt instellen op het gewenste type.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Het subdomein toevoegen en het verificatie type weer geven

1. Gebruik Power shell om het nieuwe subdomein met het standaard verificatie type van het hoofd domein toe te voegen. De beheer centrums van Azure AD en Microsoft 365 ondersteunen deze bewerking nog niet.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Gebruik [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net) om het domein op te halen. Omdat het domein geen hoofd domein is, neemt het het verificatie type van het hoofd domein over. Uw opdracht en resultaten kunnen er als volgt uitzien, met behulp van uw eigen Tenant-ID:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>De API van Azure AD Graph Explorer gebruiken om dit hoofd domein te maken

Gebruik de volgende opdracht om het subdomein te promo veren:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Het verificatie type van het subdomein wijzigen

1. Gebruik de volgende opdracht om het verificatie type subdomein te wijzigen:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Controleer via GET in azure AD Graph Explorer dat het verificatie type subdomein nu wordt beheerd:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste domeinnamen toevoegen](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Domeinnamen beheren](domains-manage.md)
- [Een aangepaste domein naam ForceDelete met Microsoft Graph-API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)