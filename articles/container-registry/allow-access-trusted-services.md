---
title: Toegang tot een register met beperkte netwerktoegang met behulp van een vertrouwde Azure-service
description: Een vertrouwd Azure-service-exemplaar veilig toegang geven tot een containerregister met beperkte netwerktoegang om afbeeldingen op te halen of te pushen
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 4b0d7feb223bcfcec4e8b2c786b211f4e3c3c3eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785866"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Vertrouwde services veilig toegang geven tot een containerregister met beperkte netwerktoegang (preview)

Azure Container Registry kunt bepaalde vertrouwde Azure-services toegang verlenen tot een register dat is geconfigureerd met regels voor netwerktoegang. Wanneer vertrouwde services zijn toegestaan, kan een vertrouwd service-exemplaar de netwerkregels van het register veilig omzeilen en bewerkingen uitvoeren zoals pull- of push-afbeeldingen. De beheerde identiteit van het service-exemplaar wordt gebruikt voor toegang en moet een Azure-rol toegewezen krijgen en worden geverifieerd bij het register.

Gebruik de Azure Cloud Shell of een lokale installatie van de Azure CLI om de opdrachtvoorbeelden in dit artikel uit te voeren. Als u deze lokaal wilt gebruiken, is versie 2.18 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Registertoegang door vertrouwde Azure-services toestaan is een **preview-functie.**

## <a name="limitations"></a>Beperkingen

* U moet een door het systeem toegewezen beheerde identiteit gebruiken die is ingeschakeld in een [vertrouwde service](#trusted-services) om toegang te krijgen tot een containerregister met beperkte netwerktoegang. Door de gebruiker toegewezen beheerde identiteiten worden momenteel niet ondersteund.
* Het toestaan van vertrouwde services is niet van toepassing op een containerregister dat is geconfigureerd met een [service-eindpunt](container-registry-vnet.md). De functie is alleen van invloed op registers die zijn beperkt met een [privé-eindpunt](container-registry-private-link.md) of waarvoor openbare [IP-toegangsregels zijn](container-registry-access-selected-networks.md) toegepast. 

## <a name="about-trusted-services"></a>Over vertrouwde services

Azure Container Registry heeft een gelaagd beveiligingsmodel dat ondersteuning biedt voor meerdere netwerkconfiguraties die de toegang tot een register beperken, waaronder:

* [Privé-eindpunt met Azure Private Link](container-registry-private-link.md). Wanneer dit is geconfigureerd, is het privé-eindpunt van een register alleen toegankelijk voor resources in het virtuele netwerk, met behulp van privé-IP-adressen.  
* [Registerfirewallregels,](container-registry-access-selected-networks.md)die alleen toegang tot het openbare eindpunt van het register toestaan vanuit specifieke openbare IP-adressen of adresbereiken. U kunt de firewall ook configureren om alle toegang tot het openbare eindpunt te blokkeren wanneer u privé-eindpunten gebruikt.

Wanneer een register is geïmplementeerd in een virtueel netwerk of is geconfigureerd met firewallregels, wordt de toegang door een register standaard geblokkeerd voor gebruikers of services van buiten deze bronnen. 

Verschillende Azure-services met meerdere tenants werken vanuit netwerken die niet kunnen worden opgenomen in deze registernetwerkinstellingen, waardoor ze geen afbeeldingen naar het register kunnen halen of pushen. Door bepaalde service-exemplaren aan te geven als 'vertrouwd', kan een registereigenaar bepaalde Azure-resources toestaan om de netwerkinstellingen van het register veilig te omzeilen voor het pullen of pushen van afbeeldingen. 

### <a name="trusted-services"></a>Vertrouwde services

Instanties van de volgende services hebben toegang tot een containerregister met beperkte netwerktoegang als de instelling Vertrouwde **services** toestaan van het register is ingeschakeld (de standaardinstelling). Na een periode worden er meer services toegevoegd.

|Vertrouwde service  |Ondersteunde gebruiksscenario's  |
|---------|---------|
|ACR-taken     | [Toegang tot een ander register vanuit een ACR-taak](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | [Een](../machine-learning/how-to-deploy-custom-docker-image.md) model [implementeren](../machine-learning/how-to-train-with-custom-image.md) of trainen in een Machine Learning werkruimte met behulp van een aangepaste Docker-containerafbeelding |
|Azure Container Registry | [Afbeeldingen importeren uit een ander Azure-containerregister](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Wanneer u de instelling Vertrouwde services toestaan inschakelen, zijn exemplaren van andere beheerde Azure-services, waaronder App Service, Azure Container Instances en Azure Security Center, niet toegestaan om toegang te krijgen tot een containerregister met beperkte netwerktoegang.

## <a name="allow-trusted-services---cli"></a>Vertrouwde services toestaan - CLI

De instelling Vertrouwde services toestaan is standaard ingeschakeld in een nieuw Azure-containerregister. Schakel de instelling uit of schakel deze in door de [opdracht az acr update uit te](/cli/azure/acr#az_acr_update) voeren.

Uitschakelen:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

De instelling inschakelen in een bestaand register of een register waar het al is uitgeschakeld:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Vertrouwde services toestaan - portal

De instelling Vertrouwde services toestaan is standaard ingeschakeld in een nieuw Azure-containerregister. 

De instelling in de portal uitschakelen of opnieuw inschakelen:

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **onder Instellingen** de optie **Netwerken.** 
1. In **Openbare netwerktoegang toestaan selecteert** u **Geselecteerde netwerken** of **Uitgeschakeld.**
1. Voer een van de volgende handelingen uit:
    * Als u toegang door vertrouwde services wilt uitschakelen, schakelt u onder **Firewall-uitzondering** het selectievakje Vertrouwde Microsoft-services **toegang tot dit containerregister uit.** 
    * Als u vertrouwde services wilt toestaan, gaat u **onder Firewall-uitzondering** naar **Vertrouwde Microsoft-services toegang tot dit containerregister toestaan.**
1. Selecteer **Opslaan**.

## <a name="trusted-services-workflow"></a>Werkstroom voor vertrouwde services

Hier is een typische werkstroom waarmee een exemplaar van een vertrouwde service toegang kan krijgen tot een containerregister met beperkte netwerktoegang.

1. Schakel een door het systeem toegewezen beheerde identiteit in voor [Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in een exemplaar van een van de vertrouwde [services](#trusted-services) voor Azure Container Registry.
1. Wijs de identiteit een [Azure-rol toe](container-registry-roles.md) aan uw register. Wijs bijvoorbeeld de rol ACRPull toe om containerafbeeldingen op te halen.
1. Configureer in het register met netwerkbeperking de instelling om toegang door vertrouwde services toe te staan.
1. Gebruik de referenties van de identiteit om te verifiëren bij het register met beperkte netwerkgegevens. 
1. Haal afbeeldingen op uit het register of voer andere bewerkingen uit die zijn toegestaan door de rol.

### <a name="example-acr-tasks"></a>Voorbeeld: ACR-taken

In het volgende voorbeeld wordt het gebruik ACR-taken als een vertrouwde service gedemonstreerd. Zie [Verificatie tussen registers in een ACR-taak met behulp van een door Azure beheerde identiteit](container-registry-tasks-cross-registry-authentication.md) voor taakdetails.

1. Maak of werk een Azure-containerregister bij en [push een voorbeeld van een basisafbeelding](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) naar het register. Dit register is het *basisregister* voor het scenario.
1. Definieer en [](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) maak [](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) in een tweede Azure-containerregister een ACR-taak om een afbeelding op te halen uit het basisregister. Schakel een door het systeem toegewezen beheerde identiteit in bij het maken van de taak.
1. Wijs de taakidentiteit [een Azure-rol toe voor toegang tot het basisregister](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Wijs bijvoorbeeld de rol AcrPull toe, die machtigingen heeft om afbeeldingen op te halen.
1. [Voeg beheerde identiteitsreferenties toe](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) aan de taak.
1. Schakel openbare toegang [in](container-registry-access-selected-networks.md#disable-public-network-access) het basisregister uit om te bevestigen dat de taak netwerkbeperkingen omzeilt.
1. Voer de taak uit. Als het basisregister en de taak correct zijn geconfigureerd, wordt de taak correct uitgevoerd, omdat het basisregister toegang toestaat.

Als u het uitschakelen van toegang door vertrouwde services wilt testen:

1. Schakel in het basisregister de instelling uit om toegang door vertrouwde services toe te staan.
1. Voer de taak opnieuw uit. In dit geval mislukt de taak, omdat het basisregister geen toegang meer toestaat door de taak.

## <a name="next-steps"></a>Volgende stappen

* Zie Configure Azure Private Link for an Azure container registry (Toegang tot een register configureren voor een [Azure-containerregister)](container-registry-private-link.md)als u de toegang tot een register wilt beperken met behulp van een privé-eindpunt in een virtueel netwerk.
* Zie Configure public IP network rules (Regels voor openbare IP-netwerken configureren) voor het instellen [van registerfirewallregels.](container-registry-access-selected-networks.md)
