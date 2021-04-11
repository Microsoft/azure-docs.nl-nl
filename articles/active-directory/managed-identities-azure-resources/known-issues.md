---
title: Bekende problemen met beheerde identiteiten-Azure Active Directory
description: Bekende problemen met beheerde identiteiten voor Azure-resources.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226919"
---
# <a name="known-issues-with-managed-identities"></a>Bekende problemen met beheerde identiteiten

In dit artikel worden enkele problemen met beheerde identiteiten beschreven en hoe u deze kunt aanpakken. Veelgestelde vragen over beheerde identiteiten worden beschreven in het artikel [Veelgestelde vragen](managed-identities-faq.md) .
## <a name="vm-fails-to-start-after-being-moved"></a>De VM kan niet worden gestart nadat deze is verplaatst 

Als u een virtuele machine verplaatst in een actieve status van een resource groep of-abonnement, blijft deze tijdens de verplaatsing actief. Als de virtuele machine eenmaal is gestopt en opnieuw wordt gestart, kan deze echter niet worden gestart. Dit probleem doet zich voor omdat de virtuele machine de verwijzing naar de beheerde identiteiten voor de Azure-resources-identiteit niet bijwerkt en blijft verwijzen naar deze in de oude resource groep.

**Tijdelijke oplossing** 
 
Activeer een update op de VM zodat deze de juiste waarden kan ophalen voor de beheerde identiteiten voor Azure-resources. U kunt een wijziging van de VM-eigenschap uitvoeren om de verwijzing naar de beheerde identiteiten voor de Azure-resources-identiteit bij te werken. U kunt bijvoorbeeld een nieuwe label waarde op de VM instellen met de volgende opdracht:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Met deze opdracht wordt een nieuwe tag ' fixVM ' ingesteld met de waarde 1 in de virtuele machine. 
 
Door deze eigenschap in te stellen, wordt de VM bijgewerkt met de juiste beheerde identiteiten voor de resource-URI van Azure resources. vervolgens moet u de virtuele machine kunnen starten. 
 
Zodra de VM is gestart, kan de tag worden verwijderd met behulp van de volgende opdracht:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Een abonnement verplaatsen tussen Azure AD-directory's

Beheerde identiteiten worden niet bijgewerkt wanneer een abonnement wordt verplaatst of overgedragen naar een andere map. Als gevolg hiervan worden bestaande door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteiten verbroken. 

Tijdelijke oplossing voor beheerde identiteiten in een abonnement dat is verplaatst naar een andere map:

 - Voor door het systeem toegewezen beheerde identiteiten: uitschakelen en opnieuw inschakelen. 
 - Voor door de gebruiker toegewezen beheerde identiteiten: verwijderen, opnieuw maken en opnieuw koppelen aan de benodigde resources (bijvoorbeeld virtuele machines)

Raadpleeg [Een Azure-abonnement overzetten naar een andere Azure AD-map](../../role-based-access-control/transfer-subscription.md) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

U kunt ons artikel raadplegen met de [Services die beheerde identiteiten ondersteunen](services-support-managed-identities.md) en onze [Veelgestelde vragen](managed-identities-faq.md)
