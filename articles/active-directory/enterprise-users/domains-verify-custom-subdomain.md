---
title: Verificatietype subdomein wijzigen met Behulp van PowerShell en Graph - Azure Active Directory | Microsoft Docs
description: Wijzig de standaardinstellingen voor subdomeinverificatie die zijn overgenomen van de hoofddomeininstellingen in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4acf01a6672d17e62b6ebf5c6f43c8d6145f95a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739310"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Verificatietype subdomein wijzigen in Azure Active Directory

Nadat een hoofddomein is toegevoegd aan Azure Active Directory (Azure AD), nemen alle volgende subdomeinen die zijn toegevoegd aan die hoofdmap in uw Azure AD-organisatie automatisch de verificatie-instelling over van het hoofddomein. Als u de instellingen voor domeinverificatie echter onafhankelijk van de instellingen van het hoofddomein wilt beheren, kunt u nu de Microsoft Graph API. Als u bijvoorbeeld een federatief hoofddomein hebt, zoals contoso.com, kan dit artikel u helpen bij het verifiëren van een subdomein, zoals child.contoso.com beheerd in plaats van federatief.

Wanneer in de Azure AD-portal het bovenliggende domein federatief is en de  beheerder probeert een beheerd subdomein te verifiëren op de pagina Aangepaste domeinnamen, krijgt u de fout 'Kan domein niet toevoegen' met de reden 'Een of meer eigenschappen bevatten ongeldige waarden'. Als u dit subdomein probeert toe te voegen vanuit de Microsoft 365-beheercentrum, wordt er een vergelijkbare fout weergegeven. Zie Een onderliggend domein neemt geen bovenliggende domeinwijzigingen over [in Office 365, Azure of Intune voor meer](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)informatie over de fout.

## <a name="how-to-verify-a-custom-subdomain"></a>Een aangepast subdomein controleren

Omdat subdomeinen het verificatietype van het hoofddomein standaard overnemen, moet u het subdomein promoveren naar een hoofddomein in Azure AD met behulp van de Microsoft Graph, zodat u het verificatietype kunt instellen op het gewenste type.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Het subdomein toevoegen en het verificatietype weergeven

1. Gebruik PowerShell om het nieuwe subdomein toe te voegen, dat het standaardverificatietype van het hoofddomein heeft. De Azure AD- en Microsoft 365-beheercentrums bieden nog geen ondersteuning voor deze bewerking.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Gebruik [Azure AD Graph Explorer om](https://graphexplorer.azurewebsites.net) het domein op te halen. Omdat het domein geen hoofddomein is, neemt het het verificatietype van het hoofddomein over. Uw opdracht en resultaten kunnen er als volgt uitzien, met behulp van uw eigen tenant-id:

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

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Azure AD Graph Explorer-API gebruiken om dit een hoofddomein te maken

Gebruik de volgende opdracht om het subdomein te promoveren:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Het verificatietype voor het subdomein wijzigen

1. Gebruik de volgende opdracht om het verificatietype voor het subdomein te wijzigen:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Controleer via GET in Azure AD Graph Explorer of het verificatietype van het subdomein nu wordt beheerd:

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
- [ForceDelete a custom domain name with Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)