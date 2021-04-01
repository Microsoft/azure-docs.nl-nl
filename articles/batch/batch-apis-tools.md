---
title: Api's en hulpprogram ma's voor ontwikkel aars
description: Meer informatie over de API's en hulpprogramma's voor het ontwikkelen van oplossingen met de Azure Batch-service.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: 502eb08631223215933b75dca882c12c02d17bd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89146399"
---
# <a name="overview-of-batch-apis-and-tools"></a>Overzicht van Batch-API's en -hulpprogramma's

Het verwerken van parallelle workloads met Azure Batch gebeurt gewoonlijk via een programma met behulp van een van de Batch-API's. Uw clienttoepassing of -service kan de Batch-API's gebruiken om met de Batch-service te communiceren. Met de Batch-API's kunt u pools van rekenknooppunten maken en beheren, ofwel virtuele machines ofwel cloudservices. Vervolgens kunt u jobs en taken plannen voor uitvoering op deze knooppunten.

U kunt voor uw organisatie efficiënt grootschalige workloads verwerken of uw klanten een front-endservice aanbieden, zodat zij (op aanvraag of gepland) jobs en taken kunnen uitvoeren op één knooppunt of honderden of zelfs duizenden knooppunten. U kunt Azure Batch ook gebruiken als onderdeel van een grotere werkstroom, beheerd door hulpprogramma's zoals [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Zie [batch-service werk stroom en resources](batch-service-workflow-features.md)voor meer informatie over de functies en werk stromen die worden gebruikt in azure batch.

## <a name="azure-accounts-for-batch-development"></a>Azure-accounts voor Batch-ontwikkeling

Wanneer u Batch-oplossingen ontwikkelt, gebruikt u de volgende accounts in uw Azure-abonnement:

- **Batch-account** -Azure batch resources, zoals Pools, reken knooppunten, Jobs en taken, worden gekoppeld aan een Azure [batch-account](accounts.md). Als uw toepassing een aanvraag indient voor de Batch-service, verifieert deze de aanvraag met de Azure Batch-accountnaam, de URL van het account en een toegangssleutel of een Azure Active Directory-token. U kunt [een batch-account maken](batch-account-create-portal.md) in de Azure portal of via een programma.
- **Storage-account**: Batch bevat ingebouwde ondersteuning voor het werken met bestanden in [Azure Storage](../storage/index.yml). Vrijwel elk Batch-scenario gebruikt Azure Blob-opslag voor het faseren van de programma's die door de taken worden uitgevoerd en de gegevens die ze verwerken, en voor de opslag van uitvoergegevens die ze genereren. Elk batch-account wordt meestal gekoppeld aan een bijbehorend opslag account.

## <a name="service-level-and-management-level-apis"></a>Api's op service niveau en beheer niveau

Azure Batch heeft twee sets Api's: één voor het service niveau en één voor het beheer niveau. De naam is vaak vergelijkbaar, maar ze retour neren verschillende resultaten.

Alleen acties van de beheer-Api's worden bijgehouden in het activiteiten logboek. Api's op service niveau slaan de Azure Resource Management-laag (management.azure.com) over en worden niet geregistreerd.

De batch-Service-API voor het [verwijderen van een pool](/rest/api/batchservice/pool/delete) is bijvoorbeeld rechtstreeks gericht op het batch-account: `DELETE {batchUrl}/pools/{poolId}`

Terwijl de [batch-beheer-API voor het verwijderen van een pool](/rest/api/batchmanagement/pool/delete)  is gericht op de Management.Azure.com-laag: `DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>API’s voor Batch-service

Uw toepassingen en services kunnen direct REST-API-aanroepen verstrekken of een of meer van de volgende clientbibliotheken gebruiken voor het uitvoeren en beheren van uw Azure Batch-workloads.

| API | API-verwijzing | Downloaden | Zelfstudie | Codevoorbeelden | Meer informatie |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[Azure-REST API-docs](/rest/api/batchservice/) |N.v.t. |- |- | [Ondersteunde versies](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[Azure SDK voor .NET-docs](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[Zelfstudie](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [Releaseopmerkingen](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[Azure SDK voor python-docs](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[Zelfstudie](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [Leesmij](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[Azure SDK voor Java script-docs](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[Zelfstudie](batch-nodejs-get-started.md) |- | [Leesmij](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Azure SDK voor Java-docs](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [Leesmij](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>API’s voor Batch Management

De Azure Resource Manager-API's voor Batch bieden programmatisch toegang tot Batch-accounts. Met deze API's kunt u Batch-accounts, -quota, -toepassingspakketten en andere -resources programmatisch beheren via de Microsoft.Batch-provider.  

| API | API-verwijzing | Downloaden | Zelfstudie | Codevoorbeelden |
| --- | --- | --- | --- | --- |
| **Batch Management REST** |[Azure-REST API-docs](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[Azure SDK voor .NET-docs](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [Zelfstudie](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Batch Management Python** |[Azure SDK voor python-docs](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Batch Management Node.js** |[Azure SDK voor Java script-docs](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Batch Management Java** |[Azure SDK voor Java-docs](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Batch-opdrachtregelprogramma's

Deze opdrachtregelprogramma's bieden dezelfde functionaliteit als de API’s voor de Batch-service en Batch Management: 

- [PowerShell-cmdlets voor Batch](/powershell/module/az.batch/): met de Azure Batch-cmdlets in de [Azure PowerShell](/powershell/azure/)-module kunt u Batch-resources beheren met PowerShell.
- [Azure CLI](/cli/azure): de Azure CLI is een platformoverschrijdende hulpmiddelenset die shellopdrachten biedt voor interactie met vele Azure-services, waaronder de Batch-service en Batch Management-service. Zie [Batch-resources beheren met de Azure CLI](batch-cli-get-started.md) voor meer informatie over het gebruik van de Azure CLI met Batch.

## <a name="other-tools-for-application-development"></a>Andere hulpmiddelen voor toepassingsontwikkeling

Deze extra hulpprogram ma's zijn mogelijk handig voor het maken en opsporen van fouten in uw batch-toepassingen en-services.

- [Azure Portal](https://portal.azure.com/): u kunt Batch-pools, -taken en -opdrachten in Azure Portal maken, controleren en verwijderen. U kunt de status informatie voor deze en andere resources bekijken terwijl u uw taken uitvoert, en zelfs bestanden downloaden van de reken knooppunten in uw Pools. U kunt bijvoorbeeld de `stderr.txt` van een taak downloaden bij het oplossen van problemen. U kunt ook Remote Desktop (RDP)-bestanden downloaden die u kunt gebruiken om aan te melden om knooppunten te berekenen.
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): Batch Explorer (vroeger BatchLabs genaamd) is een gratis, uitgebreid, zelfstandig clienthulpprogramma voor het maken en bewaken van en opsporen van fouten in Azure Batch-toepassingen. Download een [installatiepakket](https://azure.github.io/BatchExplorer/) voor Mac, Linux of Windows.
- [Azure batch Shipyard](https://github.com/Azure/batch-shipyard): batch Shipyard is een hulp programma voor het inrichten, uitvoeren en bewaken van op containers gebaseerde batch verwerking en HPC-workloads op Azure batch.
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/): Hoewel er geen Azure batch-hulp programma is, is de Storage Explorer nog een waardevol hulp middel om uw batch-oplossingen te ontwikkelen en fouten op te sporen.

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie [batch-metrische gegevens, waarschuwingen en logboeken voor diagnostische evaluatie en controle voor](batch-diagnostics.md)meer informatie over het vastleggen van gebeurtenissen in uw batch-toepassing.
- Zie [batch Analytics](batch-analytics.md)voor naslag informatie over gebeurtenissen die door de batch-service worden veroorzaakt.
- Zie [Azure batch runtime environment Varia bles](batch-compute-node-environment-variables.md)(Engelstalig) voor meer informatie over omgevings variabelen voor reken knooppunten.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Lees [Aan de slag met de Azure Batch-bibliotheek voor .NET](tutorial-parallel-dotnet.md) voor informatie over het gebruik van C# en de Batch .NET-bibliotheek om een eenvoudige workload uit te voeren met een gebruikelijke Batch-werkstroom. Een [Python-versie](tutorial-parallel-python.md) en een [Node.js-zelfstudie ](batch-nodejs-get-started.md) zijn ook beschikbaar.
- Download de [codevoorbeelden op GitHub](https://github.com/Azure-Samples/azure-batch-samples) om te zien hoe C# en Python kunnen samenwerken met Batch om voorbeeldworkloads te plannen en te verwerken.
