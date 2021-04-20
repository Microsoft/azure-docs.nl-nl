---
title: Een toegangsbeleid voor Azure Key Vault toewijzen (portal)
description: Het gebruik van de Azure Portal om een Key Vault toegangsbeleid toe te wijzen aan een beveiligingsprincipaal of toepassings-id.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 443b269e7155fc206ee50e7907a7acded2c22f53
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751486"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Key Vault-toegangsbeleid toewijzen met behulp van Azure Portal

Een Key Vault-toegangsbeleid bepaalt of een bepaalde beveiligingsprincipaal, [namelijk](../secrets/index.yml)een gebruiker, toepassing of gebruikersgroep, verschillende bewerkingen kan uitvoeren op Key Vault [geheimen,](../keys/index.yml)sleutels en [certificaten.](../certificates/index.yml) U kunt toegangsbeleid toewijzen met behulp van de Azure Portal (dit artikel), [de Azure CLI](assign-access-policy-cli.md)of [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Zie Een basisgroep maken en leden toevoegen Azure Active Directory meer [](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) informatie over het maken van groepen in Azure Portal

## <a name="assign-an-access-policy"></a>Een toegangsbeleid toewijzen

1.  [Navigeer in Azure Portal](https://portal.azure.com)naar de Key Vault resource. 

1.  Selecteer **onder Instellingen** de optie **Toegangsbeleid** en selecteer **vervolgens Toegangsbeleid toevoegen:**

    ![Selecteer Toegangsbeleid en selecteer Roltoewijzing toevoegen](../media/authentication/assign-policy-portal-01.png)

1.  Selecteer de machtigingen die u wilt onder **Certificaatmachtigingen,** **Sleutelmachtigingen** en **Geheime machtigingen.** U kunt ook een sjabloon selecteren die veelgebruikte machtigingscombinaties bevat:

    ![Machtigingen voor toegangsbeleid opgeven](../media/authentication/assign-policy-portal-02.png)

1. Kies **onder Principal selecteren** de koppeling Geen **geselecteerd** om het selectiedeelvenster **Principal** te openen. Voer de naam van de gebruiker, app of service-principal in het zoekveld in, selecteer het juiste resultaat en kies **vervolgens Selecteren.**

    ![De beveiligingsprincipaal voor het toegangsbeleid selecteren](../media/authentication/assign-policy-portal-03.png)

    Als u een beheerde identiteit voor de app gebruikt, zoekt en selecteert u de naam van de app zelf. (Zie Verificatie van Key Vault - app-identiteit en service-principals voor meer informatie over beheerde identiteiten [en service-principals.)](authentication.md#app-identity-and-security-principals)
 
1.  Selecteer in het **deelvenster Toegangsbeleid toevoegen** de optie **Toevoegen om** het toegangsbeleid op te slaan.

    ![Het toegangsbeleid toevoegen met de toegewezen beveiligingsprincipaal](../media/authentication/assign-policy-portal-04.png)

1. Controleer op de **pagina Toegangsbeleid** of uw toegangsbeleid wordt vermeld onder **Huidig** toegangsbeleid en selecteer **vervolgens Opslaan.** Toegangsbeleid wordt pas toegepast als u ze hebt op slaan.

    ![Wijzigingen in het toegangsbeleid opslaan](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](security-overview.md#identity-management)
- [Beveilig uw sleutelkluis.](security-overview.md)
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)