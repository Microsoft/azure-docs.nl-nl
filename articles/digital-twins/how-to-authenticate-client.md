---
title: Verificatiecode voor app schrijven
titleSuffix: Azure Digital Twins
description: Zie verificatiecode schrijven in een clienttoepassing
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 958b0de97b79b447f2570dd9c57c87f380bcd551
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589329"
---
# <a name="write-client-app-authentication-code"></a>Verificatiecode voor client-apps schrijven

Nadat u [een Azure Digital Twins](how-to-set-up-instance-portal.md)en verificatie hebt ingesteld, kunt u een clienttoepassing maken die u gaat gebruiken om te communiceren met het exemplaar. Zodra u een startersclientproject hebt ingesteld, moet u code schrijven in die **client-app** om deze te verifiëren aan de Azure Digital Twins exemplaar.

Azure Digital Twins voert verificatie uit met [behulp van Azure AD-beveiligingstokens op basis van OAUTH 2.0.](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) Als u uw SDK wilt verifiëren, moet u een Bearer-token met de juiste machtigingen voor Azure Digital Twins en deze samen met uw API-aanroepen doorgeven. 

In dit artikel wordt beschreven hoe u referenties kunt verkrijgen met behulp van de `Azure.Identity` clientbibliotheek. Hoewel in dit artikel codevoorbeelden in C# worden beschreven, zoals wat u schrijft voor de [.NET -SDK (C#),](/dotnet/api/overview/azure/digitaltwins/client)kunt u een versie gebruiken van ongeacht welke SDK u gebruikt `Azure.Identity` (zie [*How-to: Use the Azure Digital Twins APIs and SDKs*](how-to-use-apis-sdks.md)(De Azure Digital Twins-API's en SDK's gebruiken) voor meer informatie over de SDK's die beschikbaar zijn voor Azure Digital Twins.

## <a name="prerequisites"></a>Vereisten

Voltooi eerst de installatiestappen in [*How-to: Set up an instance and authentication*](how-to-set-up-instance-portal.md). Dit zorgt ervoor dat u een Azure Digital Twins hebt en dat uw gebruiker toegangsmachtigingen heeft. Na deze installatie bent u klaar om de code van de client-app te schrijven.

Als u wilt doorgaan, hebt u een client-app-project nodig waarin u uw code schrijft. Als u nog geen client-app-project hebt ingesteld, maakt u een basisproject in de taal van uw keuze voor gebruik met deze zelfstudie.

## <a name="common-authentication-methods-with-azureidentity"></a>Algemene verificatiemethoden met Azure.Identity

`Azure.Identity` is een clientbibliotheek die verschillende methoden voor het verkrijgen van referenties biedt die u kunt gebruiken om een Bearer-token op te halen en te verifiëren met uw SDK. Hoewel dit artikel voorbeelden in C# bevat, kunt u voor verschillende `Azure.Identity` talen weergeven, waaronder...

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

Drie veelvoorkomende methoden voor het verkrijgen van referenties in `Azure.Identity` zijn:

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) biedt een standaardverificatiestroom voor toepassingen die worden geïmplementeerd in Azure en is de aanbevolen keuze `TokenCredential` **voor lokale ontwikkeling.** Het kan ook worden ingeschakeld om de andere twee methoden uit te proberen die in dit artikel worden aanbevolen; deze wordt verpakt `ManagedIdentityCredential` en heeft toegang met een `InteractiveBrowserCredential` configuratievariabele.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) werkt goed in gevallen waarin u beheerde identiteiten [(MSI)](../active-directory/managed-identities-azure-resources/overview.md)nodig hebt en een goede kandidaat is voor het werken met Azure Functions en implementatie in Azure-services.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) is bedoeld voor interactieve toepassingen en kan worden gebruikt om een geverifieerde SDK-client te maken

In het volgende voorbeeld ziet u hoe u elk van deze kunt gebruiken met de .NET -SDK (C#).

## <a name="authentication-examples-net-c-sdk"></a>Voorbeelden van verificatie: .NET (C#) SDK

In deze sectie ziet u een voorbeeld in C# voor het gebruik van de opgegeven .NET SDK voor het schrijven van verificatiecode.

Neem eerst het SDK-pakket `Azure.DigitalTwins.Core` en het pakket op in uw `Azure.Identity` project. Afhankelijk van de hulpprogramma's van uw keuze, kunt u de pakketten opnemen met behulp van Visual Studio-pakketbeheer of het `dotnet` opdrachtregelprogramma. 

U moet ook de volgende using-instructies toevoegen aan uw projectcode:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

Voeg vervolgens code toe om referenties te verkrijgen met behulp van een van de methoden in `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Methode DefaultAzureCredential

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) biedt een standaardverificatiestroom voor toepassingen die worden geïmplementeerd in Azure en is de aanbevolen keuze `TokenCredential` **voor lokale ontwikkeling.**

Als u de standaardReferenties van Azure wilt gebruiken, hebt u de URL Azure Digital Twins van het exemplaar nodig[(instructies om te zoeken).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Hier is een codevoorbeeld om een toe te `DefaultAzureCredential` voegen aan uw project:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Lokale Azure-referenties instellen

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>Methode ManagedIdentityCredential

De [methode ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) werkt goed in gevallen waarin u beheerde identiteiten [(MSI)](../active-directory/managed-identities-azure-resources/overview.md)nodig hebt, bijvoorbeeld wanneer u met Azure Functions.

Dit betekent dat u in hetzelfde project als of kunt gebruiken om een ander deel van `ManagedIdentityCredential` `DefaultAzureCredential` het project te `InteractiveBrowserCredential` verifiëren.

Als u de standaardReferenties van Azure wilt gebruiken, hebt u de URL Azure Digital Twins van het exemplaar nodig[(instructies om te zoeken).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

In een Azure-functie kunt u de referenties van de beheerde identiteit als de volgende gebruiken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>Methode InteractiveBrowserCredential

De [methode InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) is bedoeld voor interactieve toepassingen en biedt een webbrowser voor verificatie. U kunt dit gebruiken in plaats `DefaultAzureCredential` van in gevallen waarin u interactieve verificatie nodig hebt.

Als u de referenties van de interactieve browser wilt gebruiken, hebt u een **app-registratie** nodig die machtigingen heeft voor de Azure Digital Twins API's. Zie [*How-to: Create an app registration*](how-to-create-app-registration.md)(Een app-registratie maken) voor stappen voor het instellen van deze app-registratie. Zodra de app-registratie is ingesteld, hebt u...
* de toepassings-id *(client-id) van de* app-registratie [(instructies om te zoeken)](how-to-create-app-registration.md#collect-client-id-and-tenant-id)
* de directory-id *(tenant)-id van de app-registratie* [(instructies voor het vinden van](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* de AZURE DIGITAL TWINS van het exemplaar (instructies[om te zoeken)](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Hier is een voorbeeld van de code voor het maken van een geverifieerde SDK-client met behulp van `InteractiveBrowserCredential` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> Hoewel u de client-id, tenant-id en url van het exemplaar rechtstreeks in de code kunt plaatsen, zoals hierboven wordt weergegeven, is het een goed idee om uw code deze waarden op te halen uit een configuratiebestand of omgevingsvariabele.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Andere opmerkingen over het Azure Functions

Zie [*Uitleg: Een Azure-functie*](how-to-create-azure-function.md) instellen voor het verwerken van gegevens voor een vollediger voorbeeld waarin enkele van de belangrijke configuratiekeuzes in de context van functies worden uitgelegd.

Als u verificatie in een functie wilt gebruiken, moet u ook het volgende onthouden:
* [Beheerde identiteit inschakelen](../app-service/overview-managed-identity.md?tabs=dotnet)
* [Omgevingsvariabelen gebruiken](/sandbox/functions-recipes/environment-variables?tabs=csharp) waar nodig
* Wijs machtigingen toe aan de functions-app waarmee deze toegang kan krijgen tot Digital Twins API's. Zie [*How-to: Set up an Azure function*](how-to-create-azure-function.md)for processing data (Een Azure-functie instellen voor het verwerken van gegevens) voor meer informatie over de Azure Functions processen.

## <a name="authenticate-across-tenants"></a>Verifiëren in meerdere tenants

Azure Digital Twins is een service die slechts één  [Azure Active Directory -tenant (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md)ondersteunt: de hoofd-tenant van het abonnement waarin de Azure Digital Twins zich bevindt.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

Als u toegang wilt krijgen tot uw Azure Digital Twins-exemplaar met behulp van een service-principal of gebruikersaccount dat bij een andere tenant van het exemplaar hoort, kunt u elke federatief identiteit van een andere tenant een **token** laten aanvragen bij de tenant 'home' van het Azure Digital Twins-exemplaar. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

U kunt ook de thuis-tenant opgeven in de referentieopties in uw code. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>Andere referentiemethoden

Als de gemarkeerde verificatiescenario's niet voldoen aan de behoeften van uw app, kunt u andere typen verificatie verkennen die worden aangeboden in [**het Microsoft Identity Platform.**](../active-directory/develop/v2-overview.md#getting-started) De documentatie voor dit platform bevat aanvullende verificatiescenario's, geordend op toepassingstype.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe beveiliging werkt in Azure Digital Twins:
* [*Concepten: Beveiliging voor Azure Digital Twins oplossingen*](concepts-security.md)

Of, nu de verificatie is ingesteld, gaat u verder met het maken en beheren van modellen in uw instantie:
* [*How-to: Manage DTDL models (DTDL-modellen beheren)*](how-to-manage-model.md)