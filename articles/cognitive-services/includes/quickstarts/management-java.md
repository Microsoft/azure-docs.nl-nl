---
title: 'Quickstart: Azure Management-clientbibliotheek voor Java'
description: Ga in deze quickstart aan de slag met de Azure Management-clientbibliotheek voor Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 4c0d4dd1a834e42a75da5199b7aaed0e123f8e63
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879737"
---
[Referentiedocumentatie](/java/api/com.microsoft.azure.management.cognitiveservices) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18) | [Pakket (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="java-prerequisites"></a>Java-vereisten

* Een geldig Azure-abonnement - [Maak een gratis abonnement](https://azure.microsoft.com/free/).
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder.


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>Een nieuwe Java-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `gradle init` uit vanuit uw werkmap. Met deze opdracht maakt u essentiële buildbestanden voor Gradle, inclusief *build.gradle.kts*, dat tijdens runtime wordt gebruikt om de toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

Voer de volgende opdracht uit vanuit uw werkmap:

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Deze quickstart maakt gebruik van de Gradle-afhankelijkheidsmanager. U vindt de clientbibliotheek en informatie voor andere afhankelijkheidsbeheerders in de [Maven Central Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

Zorg ervoor dat u in het bestand *build.gradle.kts* de clientbibliotheek opneemt als een `implementation`-instructie, samen met de vereiste invoegtoepassen en instellingen.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>Bibliotheken importeren

Ga naar de nieuwe map **src/main/java** en maak een bestand met de naam *Management.java*. Open het bestand in uw voorkeurseditor of IDE en voeg de volgende `import`-instructies toe:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>De client verifiëren

Voeg een klasse toe in *Management.java* en voeg de volgende velden en hun waarden toe. Vul de waarden in met behulp van de service-principal die u hebt gemaakt en uw andere Azure-accountgegevens.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

Gebruik deze waarden vervolgens in uw **main**-methode om een **CognitiveServicesManager**-object te maken. Dit object is nodig voor al uw Azure Management-bewerkingen.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>Methoden voor aanroepbeheer

Voeg de volgende code toe aan uw **Main**-methode om beschikbare resources weer te geven, maak een voorbeeldresource, vermeld uw eigen resources en verwijder vervolgens de voorbeeldresource. U gaat deze methoden in de volgende stappen definiëren.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-java"></a>Een Cognitive Services-resource maken (Java)

Als u een nieuwe Cognitive Services-resource wilt maken en zich hierop wilt abonneren, gebruikt u de methode **maken**. Deze methode voegt een nieuwe factureerbare resource toe aan de resourcegroep die u doorgeeft. Wanneer u uw nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de prijscategorie (of SKU) en een Azure-locatie. Met de volgende methode worden al deze argumenten gebruikt en wordt een resource gemaakt.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Een service en prijscategorie kiezen

Wanneer u een nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de [prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/) (of SKU) die u wilt. U gebruikt deze en andere informatie als parameters bij het maken van de resource. U kunt een lijst met beschikbare soorten Cognitive Services vinden door de volgende methode aan te roepen:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Uw resources weergeven

Als u alle resources onder uw Azure-account (voor alle resourcegroepen) wilt weergeven, gebruikt u de volgende methode:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>Een resource verwijderen

Met de volgende methode verwijdert u de opgegeven resource uit de opgegeven resourcegroep.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>Zie ook

* Zie **[aanvragen verifiëren voor Azure Cognitive Services](../../authentication.md)** over het veilig werken met Cognitive Services.
* Zie **[Wat is Azure Cognitive Services?](../../what-are-cognitive-services.md)** voor een lijst met verschillende categorieën in cognitive Services.
* Zie **[ondersteuning voor natuurlijke](../../language-support.md)** talen voor een overzicht van de natuurlijke talen die Cognitive Services ondersteunt.
* Zie **[Cognitive Services als containers gebruiken](../../cognitive-services-container-support.md)** voor meer informatie over het gebruik van Cognitive Services on-premises.
* Zie **[kosten plannen en beheren voor Cognitive Services](../../plan-manage-costs.md)** voor het schatten van de kosten van het gebruik van Cognitive Services.
* Raadpleeg de **[naslag documentatie over Azure Management SDK](/java/api/com.microsoft.azure.management.cognitiveservices)** voor meer informatie over de Management SDK.
