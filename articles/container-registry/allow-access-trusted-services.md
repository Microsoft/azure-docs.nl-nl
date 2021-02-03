---
title: Via het netwerk beperkt REGI ster openen met behulp van een vertrouwde Azure-service
description: Een vertrouwd exemplaar van Azure service inschakelen voor een veilige toegang tot pull-of push-installatie kopieën in een container register met netwerk beperkingen
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 2e6b6ee3736f98f53ebb0aa43d707d42ba4cc058
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527670"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Vertrouwde services veilig toegang geven tot een container register in het netwerk (preview-versie)

Azure Container Registry kunt het selecteren van vertrouwde Azure-Services toestaan om toegang te krijgen tot een REGI ster dat is geconfigureerd met netwerk toegangs regels. Wanneer vertrouwde services zijn toegestaan, kan een vertrouwd service-exemplaar veilig de netwerk regels van het REGI ster passeren en bewerkingen uitvoeren, zoals pull-of push installatie kopieën. De beheerde identiteit van het service-exemplaar wordt gebruikt voor toegang en moet worden toegewezen aan een Azure-rol en moeten worden geverifieerd met het REGI ster.

Gebruik de Azure Cloud Shell of een lokale installatie van de Azure CLI om de opdracht voorbeelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2,18 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Het toestaan van toegang tot het REGI ster door betrouw bare Azure-Services is een **Preview** -functie.

## <a name="limitations"></a>Beperkingen

* U moet een door het systeem toegewezen beheerde identiteit gebruiken die is ingeschakeld in een [vertrouwde service](#trusted-services) om toegang te krijgen tot een container register in het netwerk waarvoor beperkingen gelden. Door de gebruiker toegewezen beheerde identiteiten worden momenteel niet ondersteund.
* Het toestaan van vertrouwde services is niet van toepassing op een container register dat is geconfigureerd met een [service-eind punt](container-registry-vnet.md). De functie is alleen van invloed op registers die zijn beperkt met een [persoonlijk eind punt](container-registry-private-link.md) of waarvoor [open bare IP-toegangs regels](container-registry-access-selected-networks.md) zijn toegepast. 

## <a name="about-trusted-services"></a>Over vertrouwde services

Azure Container Registry heeft een gelaagd beveiligings model dat meerdere netwerk configuraties ondersteunt die de toegang tot een REGI ster beperken, waaronder:

* [Persoonlijk eind punt met persoonlijke Azure-koppeling](container-registry-private-link.md). Indien geconfigureerd, is het persoonlijke eind punt van een REGI ster alleen toegankelijk voor bronnen in het virtuele netwerk met behulp van privé-IP-adressen.  
* De [firewall regels](container-registry-access-selected-networks.md)van het REGI ster, waarmee toegang tot het open bare eind punt van het REGI ster alleen kan worden toegestaan vanuit specifieke open bare IP-adressen of adresbereiken. U kunt de firewall ook zo configureren dat alle toegang tot het open bare eind punt wordt geblokkeerd bij het gebruik van privé-eind punten.

Wanneer in een virtueel netwerk is geïmplementeerd of is geconfigureerd met firewall regels, weigert een REGI ster standaard de toegang voor gebruikers of services van buiten die bronnen. 

Verschillende Azure-Services voor meerdere tenants werken met netwerken die niet kunnen worden opgenomen in deze register instellingen, waardoor er geen installatie kopieën naar het REGI ster kan worden opgehaald of gepusht. Door bepaalde service-instanties als ' vertrouwd ' aan te wijzen, kan een register eigenaar selecteren dat Azure-resources de netwerk instellingen van het REGI ster veilig mogen omzeilen om installatie kopieën te halen of te pushen. 

### <a name="trusted-services"></a>Vertrouwde services

Instanties van de volgende services hebben toegang tot een container register waarvoor een netwerk beperking geldt als de instelling **vertrouwde services** van het REGI ster is ingeschakeld (de standaard waarde). Meer services worden na verloop van tijd toegevoegd.

|Vertrouwde service  |Ondersteunde gebruiks scenario's  |
|---------|---------|
|ACR-taken     | [Toegang tot een ander REGI ster vanuit een ACR-taak](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | Een model [implementeren](../machine-learning/how-to-deploy-custom-docker-image.md) of [trainen](../machine-learning/how-to-train-with-custom-image.md) in een machine learning-werk ruimte met behulp van een aangepaste docker-container installatie kopie |
|Azure Container Registry | [Installatie kopieën importeren uit een ander Azure container Registry](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Curently, waardoor de instelling vertrouwde services toestaan geen instanties van andere beheerde Azure-Services toestaat, waaronder App Service, Azure Container Instances en Azure Security Center voor toegang tot een container register met een beperkt netwerk.

## <a name="allow-trusted-services---cli"></a>Vertrouwde services toestaan-CLI

Standaard is de instelling vertrouwde services toestaan ingeschakeld in een nieuw Azure container Registry. Schakel de instelling in of uit door de opdracht [AZ ACR update](/cli/azure/acr#az-acr-update) uit te voeren.

Uitschakelen:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

De instelling inschakelen in een bestaand REGI ster of in een REGI ster waar deze al is uitgeschakeld:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Vertrouwde services toestaan-Portal

Standaard is de instelling vertrouwde services toestaan ingeschakeld in een nieuw Azure container Registry. 

Als u de instelling in de portal wilt uitschakelen of opnieuw wilt inschakelen:

1. Navigeer in de portal naar het container register.
1. Selecteer onder **instellingen** de optie **netwerken**. 
1. Selecteer in **open bare netwerk toegang toestaan** **geselecteerde netwerken** of **uitgeschakeld**.
1. Voer een van de volgende handelingen uit:
    * **Schakel** het selectie vakje **vertrouwde micro soft-Services toestaan om toegang te krijgen tot dit container register** uit om toegang door vertrouwde services uit te scha kelen. 
    * Als u vertrouwde services wilt toestaan, onder **firewall uitzondering**, schakelt **u vertrouwde micro soft-Services toestaan om toegang te krijgen tot dit container register**.
1. Selecteer **Opslaan**.

## <a name="trusted-services-workflow"></a>Werk stroom voor vertrouwde services

Hier volgt een typische werk stroom voor het inschakelen van een exemplaar van een vertrouwde service om toegang te krijgen tot een container register in het netwerk waarvoor beperkingen gelden.

1. Een door het systeem toegewezen [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) inschakelen in een exemplaar van een van de [vertrouwde services](#trusted-services) voor Azure container Registry.
1. Wijs de identiteit een [Azure-rol](container-registry-roles.md) toe aan het REGI ster. Wijs bijvoorbeeld de rol ACRPull toe voor het ophalen van container installatie kopieën.
1. Configureer in het REGI ster met netwerk beperkingen de instelling om toegang door vertrouwde services toe te staan.
1. Gebruik de referenties van de identiteit voor verificatie met het door het netwerk beperkt REGI ster. 
1. Haal installatie kopieën op uit het REGI ster of voer andere bewerkingen uit die zijn toegestaan door de rol.

### <a name="example-acr-tasks"></a>Voor beeld: ACR-taken

In het volgende voor beeld ziet u hoe u ACR-taken als een vertrouwde service gebruikt. Zie [Cross-Registry-verificatie in een ACR-taak met behulp van een door Azure beheerde identiteit](container-registry-tasks-cross-registry-authentication.md) voor taak Details.

1. Een Azure container Registry maken of bijwerken en [een voor beeld van een basis installatie kopie](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) naar het REGI ster pushen. Dit REGI ster is het *basis register* voor het scenario.
1. In een tweede Azure container Registry [definieert](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) en [maakt](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) u een ACR-taak om een installatie kopie uit het basis register te halen. Een door het systeem toegewezen beheerde identiteit inschakelen bij het maken van de taak.
1. Wijs de taak-id [een Azure-rol toe om toegang te krijgen tot het basis register](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Wijs bijvoorbeeld de rol AcrPull toe, die machtigingen heeft voor het ophalen van installatie kopieën.
1. [Referenties voor beheerde identiteit toevoegen](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) aan de taak.
1. Als u wilt controleren of de taak netwerk beperkingen omzeilt, [schakelt u open bare toegang](container-registry-access-selected-networks.md#disable-public-network-access) uit in het basis register.
1. Voer de taak uit. Als het basis register en de taak correct zijn geconfigureerd, wordt de taak uitgevoerd, omdat het basis register toegang toestaat.

Het uitschakelen van de toegang door vertrouwde services testen:

1. Schakel in het basis register de instelling uit om toegang door vertrouwde services toe te staan.
1. Voer de taak opnieuw uit. In dit geval mislukt de uitvoering van de taak, omdat het basis register geen toegang meer biedt voor de taak.

## <a name="next-steps"></a>Volgende stappen

* Als u de toegang tot een REGI ster wilt beperken met behulp van een persoonlijk eind punt in een virtueel netwerk, raadpleegt u [Azure private link configureren voor een Azure container Registry](container-registry-private-link.md).
* Zie voor het instellen van de firewall regels voor het REGI ster [configuratie van open bare IP-netwerken configureren](container-registry-access-selected-networks.md).
