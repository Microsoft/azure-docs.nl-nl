---
title: Toegang tot de Azure VMware-oplossing via CloudSimple-Portal
description: Hierin wordt beschreven hoe u vanuit de Azure-portal de VMware Solution by CloudSimple-portal opent
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0aa7a9a1f19a9d4fb2c555b08753e0b57c657974
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895152"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Open de VMware-oplossing via CloudSimple portal van de Azure Portal

Eenmalige aanmelding wordt ondersteund voor toegang tot de CloudSimple-Portal. Nadat u zich hebt aangemeld bij de Azure Portal, kunt u toegang krijgen tot de CloudSimple-Portal zonder u opnieuw aan te melden. De eerste keer dat u de CloudSimple-Portal opent, wordt u gevraagd de [CloudSimple service-autorisatie](#consent-to-cloudsimple-service-authorization-application) toepassing te autoriseren.  Autorisatie is een eenmalige actie.

## <a name="before-you-begin"></a>Voordat u begint

Gebruikers met rollen van de ingebouwde **eigenaar** en **Inzender** hebben toegang tot de CloudSimple-Portal.  De functies moeten worden geconfigureerd voor de resource groep waar de CloudSimple-service wordt geïmplementeerd.  De functies kunnen ook worden geconfigureerd op het CloudSimple-Service object.  Zie het artikel [roltoewijzingen weer geven](../role-based-access-control/check-access.md) voor meer informatie over het controleren van uw rol. Alleen gebruikers met ingebouwde rollen **eigenaar** en **Inzender** hebben toegang tot de CloudSimple-Portal.  De rollen moeten worden geconfigureerd in het abonnement.  Zie het artikel [roltoewijzingen weer geven](../role-based-access-control/check-access.md) voor meer informatie over het controleren van uw rol.

Als u aangepaste rollen gebruikt, moet de rol een van de volgende bewerkingen onder bevatten ```Actions``` .  Zie aangepaste rollen voor [Azure](../role-based-access-control/custom-roles.md)voor meer informatie over aangepaste rollen.  Als een van de bewerkingen deel van uitmaakt ```NotActions``` , heeft de gebruiker geen toegang tot de CloudSimple-Portal.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

1. Selecteer **Alle services**.

2. Zoek naar **CloudSimple Services**.

3. Selecteer de CloudSimple-service waarop u uw Privécloud wilt maken.

4. Klik op de pagina **overzicht** op **Ga naar de CloudSimple-Portal**.  Als u de CloudSimple-portal van de Azure Portal voor het eerst gebruikt, wordt u gevraagd om de [CloudSimple-service autorisatie](#consent-to-cloudsimple-service-authorization-application) toepassing te autoriseren. 

    ![CloudSimple-Portal starten](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Als u een privé-Cloud bewerking (zoals het maken of uitbreiden van een Privécloud) rechtstreeks vanuit het Azure Portal selecteert, wordt de CloudSimple-portal geopend op de aangegeven pagina.

Selecteer in de CloudSimple-Portal **Home** in het menu aan de zijkant om samenvattende informatie over uw persoonlijke Clouds weer te geven. De resources en capaciteit van uw persoonlijke Clouds worden weer gegeven, samen met waarschuwingen en taken waarvoor aandacht is vereist. Voor algemene taken klikt u op de benoemde pictogrammen boven aan de pagina.

![Start pagina](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Toestemming geven aan CloudSimple-service autorisatie toepassing

Voor het starten van de CloudSimple-Portal vanuit de Azure Portal voor de eerste keer moet u toestemming hebben voor de CloudSimple service-autorisatie toepassing.  Selecteer **accepteren** om aangevraagde machtigingen te verlenen en toegang te krijgen tot de CloudSimple-Portal.

![Toestemming geven voor CloudSimple-service autorisatie-beheerders](media/cloudsimple-azure-consent.png)

Als u globale beheerders rechten hebt, kunt u toestemming geven voor uw organisatie.  Selecteer **Toestemming namens uw organisatie**.

![Toestemming geven voor CloudSimple-service autorisatie-globale beheerder](media/cloudsimple-azure-consent-global-admin.png)

Als uw machtigingen geen toegang tot de CloudSimple-Portal toestaan, neemt u contact op met de globale beheerder van uw Tenant om de vereiste machtigingen te verlenen.  Een globale beheerder kan namens uw organisatie toestemming geven.

![Toestemming voor CloudSimple-service autorisatie: vereist beheerders](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een privécloud](./create-private-cloud.md)
* Meer informatie over het [configureren van een privécloud](quickstart-create-private-cloud.md)
