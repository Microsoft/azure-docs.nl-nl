---
title: Door het systeem toegewezen beheerde identiteit inschakelen voor de Azure Spring Cloud-toepassing
description: Het inschakelen van door het systeem toegewezen beheerde identiteit voor toepassing.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2fbdd46b872c4c70999355d457045f4aac8aa34c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877972"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Door het systeem toegewezen beheerde identiteit inschakelen voor de Azure Spring Cloud-toepassing

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Beheerde identiteiten voor Azure-resources bieden een automatisch beheerde identiteit in Azure Active Directory aan een Azure-resource, zoals uw Azure lente-Cloud toepassing. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben.

In dit artikel wordt beschreven hoe u door het systeem toegewezen beheerde identiteiten in-en uitschakelt voor een Azure lente-Cloud toepassing met behulp van de Azure Portal en CLI (beschikbaar via versie 0.2.4).

## <a name="prerequisites"></a>Vereisten
Zie [overzichts sectie](../active-directory/managed-identities-azure-resources/overview.md)als u niet bekend bent met beheerde identiteiten voor Azure-resources.
U hebt een geïmplementeerd Azure veer Cloud-exemplaar nodig. Volg de [Snelstartgids om te implementeren met behulp van de Azure cli](spring-cloud-quickstart.md).

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen
Voor het maken van een app met een door het systeem toegewezen identiteit moet u een extra eigenschap voor de toepassing instellen.

### <a name="using-azure-portal"></a>Azure Portal gebruiken
Als u een beheerde identiteit in de [Azure Portal](https://portal.azure.com/)wilt instellen, maakt u eerst een app en schakelt u de functie in.

1. Maak een app in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.
2. Schuif omlaag naar de **instellingen** groep in het navigatie deel venster links.
3. Selecteer **identiteit**.
4. Schakel op het tabblad **systeem toegewezen** de optie **status** in *op aan*. Klik op **Opslaan**.

 ![Beheerde identiteit in de portal](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken
U kunt door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een app of voor een bestaande app.

**Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een app**

In het volgende voor beeld wordt een app met de naam *app_name* gemaakt met een door het systeem toegewezen beheerde identiteit, zoals aangevraagd door de `--assign-identity` para meter.

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**Door het systeem toegewezen beheerde identiteit inschakelen voor een bestaande app** Gebruik `az spring-cloud app identity assign` de opdracht om de door het systeem toegewezen identiteit in te scha kelen voor een bestaande app.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Tokens verkrijgen voor Azure-resources
Een app kan de beheerde identiteit gebruiken om tokens te verkrijgen voor toegang tot andere bronnen die worden beveiligd door Azure Active Directory, zoals Azure Key Vault. Deze tokens vertegenwoordigen de toepassing die toegang heeft tot de resource, niet een specifieke gebruiker van de toepassing.

Mogelijk moet u [de doel bron configureren om toegang toe te staan vanuit uw toepassing](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Als u bijvoorbeeld een token aanvraagt voor toegang tot Key Vault, moet u ervoor zorgen dat u een toegangs beleid hebt toegevoegd dat de identiteit van uw toepassing bevat. Anders worden uw aanroepen naar Key Vault geweigerd, zelfs als ze het token bevatten. Zie [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)voor meer informatie over welke bronnen Azure Active Directory-tokens ondersteunen.

Azure lente Cloud deelt hetzelfde eind punt voor het ophalen van tokens met de virtuele machine van Azure. We raden u aan om een token op te halen met behulp van Java SDK of lente boot-starters.  Zie [het gebruik](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) van een VM-token voor verschillende code-en script voorbeelden en richt lijnen over belang rijke onderwerpen, zoals het verwerken van token verloopt en HTTP-fouten.

Aanbevolen: gebruik de Java-SDK of veer boot starters om tokens op te halen.  Zie de voor beelden in de [volgende stappen](#next-steps).

## <a name="disable-system-assigned-identity-from-an-app"></a>Door het systeem toegewezen identiteit uit een app uitschakelen
Als u een door het systeem toegewezen identiteit verwijdert, wordt deze ook uit Azure AD verwijderd. Als u de app-resource verwijdert, worden automatisch door het systeem toegewezen identiteiten verwijderd uit Azure AD.

### <a name="using-azure-portal"></a>Azure Portal gebruiken
Door het systeem toegewezen beheerde identiteit verwijderen uit een app die niet meer nodig is:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat het Azure veer Cloud-exemplaar bevat.
1. Navigeer naar de gewenste VM en selecteer **Identiteit**.
1. Selecteer onder **systeem toegewezen** / **status** de optie **uit** en klik vervolgens op **Opslaan**:

 ![Beheerde identiteit](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken
Gebruik de volgende opdracht om door het systeem toegewezen beheerde identiteit te verwijderen uit een app die niet meer nodig is:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Volgende stappen

* [Toegang tot Azure Key Vault met beheerde identiteiten in Spring boot starter](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Meer informatie over beheerde identiteiten voor Azure-resources](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Beheerde identiteiten gebruiken met Java SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
