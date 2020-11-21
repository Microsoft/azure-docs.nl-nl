---
title: Een resource-app registreren in azure AD-Azure API voor FHIR
description: Registreer een resource-app (of API) in Azure Active Directory, zodat client toepassingen toegang tot de bron kunnen aanvragen tijdens de verificatie.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024479"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Een resource toepassing registreren in Azure Active Directory

In dit artikel leert u hoe u een resource-of API-toepassing in Azure Active Directory kunt registreren. Een resource toepassing is een Azure Active Directory representatie van de FHIR-Server-API zelf en client toepassingen kunnen tijdens de verificatie toegang tot de bron aanvragen. De resource toepassing staat ook bekend als de *doel groep* in OAuth-jargon.

## <a name="azure-api-for-fhir"></a>Azure-API voor FHIR

Als u de Azure API voor FHIR gebruikt, wordt er automatisch een resource toepassing gemaakt wanneer u de service implementeert. Zolang u de Azure-API voor FHIR in dezelfde Azure Active Directory Tenant gebruikt wanneer u uw toepassing implementeert, kunt u deze hand leiding overs Laan en in plaats daarvan uw Azure API voor FHIR implementeren om aan de slag te gaan.

Als u een andere Azure Active Directory Tenant gebruikt (niet gekoppeld aan uw abonnement), kunt u de Azure API voor de FHIR-resource toepassing importeren in uw Tenant met Power shell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

u kunt ook Azure CLI gebruiken:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR-server voor Azure

Als u gebruikmaakt van de open source FHIR-server voor Azure, volgt u de stappen in de [github-opslag plaats](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) om een resource toepassing te registreren. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een resource toepassing kunt registreren in Azure Active Directory. Registreer vervolgens uw vertrouwelijke client toepassing.
 
>[!div class="nextstepaction"]
>[Vertrouwelijke client toepassing registreren](register-confidential-azure-ad-client-app.md)