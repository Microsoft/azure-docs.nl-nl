---
title: Een Azure Key Vault toegangs beleid (Portal) toewijzen
description: Het Azure Portal gebruiken om een Key Vault toegangs beleid toe te wijzen aan een service-principal of toepassings-id.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 910b8dae10036cc2e396be13495fd28363dc971d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934557"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Key Vault-toegangsbeleid toewijzen met behulp van Azure Portal

Een Key Vault toegangs beleid bepaalt of een bepaalde service-principal, namelijk een toepassing of gebruikers groep, verschillende bewerkingen kan uitvoeren op Key Vault [geheimen](../secrets/index.yml), [sleutels](../keys/index.yml)en [certificaten](../certificates/index.yml). U kunt toegangs beleid toewijzen met behulp van de Azure Portal (dit artikel), de [Azure cli](assign-access-policy-cli.md)of [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Zie [een basis groep maken en leden toevoegen](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie over het maken van groepen in azure Active Directory via de Azure Portal.

## <a name="assign-an-access-policy"></a>Een toegangsbeleid toewijzen

1.  Ga in het [Azure Portal](https://portal.azure.com)naar de Key Vault resource. 

1.  Selecteer onder **instellingen** **toegangs beleid** en selecteer vervolgens **toegangs beleid toevoegen**:

    ![Selecteer toegangs beleid, selecteren functie toewijzing toevoegen](../media/authentication/assign-policy-portal-01.png)

1.  Selecteer de gewenste machtigingen onder **certificaat machtigingen**, **sleutel machtigingen** en **geheime machtigingen**. U kunt ook een sjabloon selecteren die algemene combi Naties van machtigingen bevat:

    ![Machtigingen voor toegangs beleid opgeven](../media/authentication/assign-policy-portal-02.png)

1. Kies onder **Principal selecteren** de koppeling **geen geselecteerd** om het deel venster voor de selectie van **principals** te openen. Voer de naam van de app of Service-Principal in het zoek veld in, selecteer het gewenste resultaat en kies vervolgens **selecteren**.

    ![De service-principal voor het toegangs beleid selecteren](../media/authentication/assign-policy-portal-03.png)

    Als u een beheerde identiteit voor de app gebruikt, zoekt en selecteert u de naam van de app zelf. (Zie [Key Vault-verificatie-app-identiteit en service-principals](authentication.md#app-identity-and-security-principals)voor meer informatie over beheerde identiteits-en service-principals.)
 
1.  Selecteer in het deel venster **toegangs beleid toevoegen** de optie **toevoegen** om het toegangs beleid op te slaan.

    ![Het toegangs beleid toevoegen aan de Service-Principal toegewezen](../media/authentication/assign-policy-portal-04.png)

1. Ga terug naar de pagina **toegangs beleid** , Controleer of uw toegangs beleid wordt weer gegeven onder **huidige toegangs beleid** en selecteer vervolgens **Opslaan**. Toegangs beleid wordt pas toegepast wanneer u ze opslaat.

    ![De wijzigingen in het toegangs beleid opslaan](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](security-overview.md#identity-management)
- [Beveilig uw sleutel kluis](secure-your-key-vault.md).
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)