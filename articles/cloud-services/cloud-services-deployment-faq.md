---
title: Implementatie problemen voor Microsoft Azure Cloud Services Veelgestelde vragen | Microsoft Docs
description: In dit artikel vindt u de veelgestelde vragen over de implementatie van Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd7b19a2c9e872b811c1aab6e504accb7de383b2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896474"
---
# <a name="deployment-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Implementatie problemen voor Azure Cloud Services (klassiek): veelgestelde vragen (FAQ)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.
In dit artikel vindt u veelgestelde vragen over implementatie problemen voor [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). U kunt ook de [pagina Cloud Services VM-grootte](cloud-services-sizes-specs.md) raadplegen voor informatie over de grootte.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Waarom is het implementeren van een Cloud service naar de staging-sleuf soms mislukt met een resource toewijzings fout als er al een bestaande implementatie in de productie sleuf is?
Als een Cloud service in een van de sleuven een implementatie heeft, wordt de volledige Cloud service vastgemaakt aan een specifiek cluster. Dit betekent dat als er al een implementatie in de productie site bestaat, een nieuwe faserings implementatie alleen kan worden toegewezen in hetzelfde cluster als de productie site.

Toewijzings fouten treden op wanneer het cluster waar uw Cloud service zich bevindt onvoldoende fysieke reken bronnen heeft om te voldoen aan uw implementatie aanvraag.

Zie [Cloud service toewijzings fout: oplossingen](cloud-services-allocation-failures.md#solutions)voor meer informatie over het beperken van dergelijke toewijzings fouten.

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Waarom wordt het omhoog of schalen van een Cloud service-implementatie in sommige gevallen ook wel de toewijzings fout veroorzaakt?
Wanneer een Cloud service is geïmplementeerd, wordt deze meestal vastgemaakt aan een specifiek cluster. Dit betekent dat het omhoog/omlaag schalen van een bestaande Cloud service nieuwe instanties in hetzelfde cluster moet toewijzen. Als het cluster de capaciteit bijna heeft of de gewenste grootte/type van de virtuele machine niet beschikbaar is, kan de aanvraag mislukken.

Zie [Cloud service toewijzings fout: oplossingen](cloud-services-allocation-failures.md#solutions)voor meer informatie over het beperken van dergelijke toewijzings fouten.

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Waarom resulteert het implementeren van een Cloud service in een affiniteits groep soms in toewijzings fout?
Een nieuwe implementatie naar een lege Cloud service kan worden toegewezen door de infra structuur in een cluster in die regio, tenzij de Cloud service is vastgemaakt aan een affiniteits groep. Implementaties voor dezelfde affiniteits groep worden geprobeerd op hetzelfde cluster. Als de capaciteit van het cluster bijna is bereikt, kan de aanvraag mislukken.

Zie [Cloud service toewijzings fout: oplossingen](cloud-services-allocation-failures.md#solutions)voor meer informatie over het beperken van dergelijke toewijzings fouten.

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Waarom resulteert het wijzigen van de VM-grootte of het toevoegen van een nieuwe virtuele machine aan een bestaande Cloud service er soms toe voor toewijzings fouten?
De clusters in een Data Center kunnen verschillende configuraties van computer typen hebben (bijvoorbeeld een serie, Av2-serie, D-serie, dv2-serie, G-serie, H-serie, enzovoort). Maar niet alle clusters zouden altijd alle typen Vm's hebben. Als u bijvoorbeeld probeert een D-serie-VM toe te voegen aan een Cloud service die al is geïmplementeerd in een cluster met alleen een reeks, zal er een toewijzings fout optreden. Dit gebeurt ook als u VM-SKU-grootten probeert te wijzigen (bijvoorbeeld het overschakelen van een serie naar een D-serie).

Zie [Cloud service toewijzings fout: oplossingen](cloud-services-allocation-failures.md#solutions)voor meer informatie over het beperken van dergelijke toewijzings fouten.

Zie [Microsoft Azure: producten beschikbaar per regio](https://azure.microsoft.com/regions/services)om de beschik bare grootten in uw regio te controleren.

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Waarom is het implementeren van een Cloud service ergens mislukt vanwege limieten/quota's/beperkingen voor mijn abonnement of service?
De implementatie van een Cloud service kan mislukken als de resources die moeten worden toegewezen, het standaard-of maximum quotum overschrijden dat is toegestaan voor uw service op het niveau van de regio/Data Center. Zie [Cloud Services limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits)voor meer informatie.

U kunt ook het huidige gebruik/quotum voor uw abonnement volgen op de portal: Azure Portal =>-abonnementen => \<appropriate subscription>   => "gebruik + quotum".

Informatie over het resource gebruik/het verbruik kan ook worden opgehaald via de Azure Billing-API's. Zie [overzicht van Azure-verbruiks-API](../cost-management-billing/manage/consumption-api-overview.md).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Hoe kan ik de grootte van een geïmplementeerde Cloud service-VM wijzigen zonder deze opnieuw te implementeren?
U kunt de VM-grootte van een geïmplementeerde Cloud service niet wijzigen zonder deze opnieuw te implementeren. De VM-grootte is ingebouwd in de CSDEF, die alleen kan worden bijgewerkt met een nieuwe implementatie.

Zie [How to update a Cloud service](cloud-services-update-azure-service.md)(Engelstalig) voor meer informatie.

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Waarom kan ik Cloud Services niet implementeren via Service Management-Api's of Power shell wanneer ik Azure Resource Manager-opslag account gebruik? 

Omdat de Cloud service een klassieke resource is die niet rechtstreeks compatibel is met het Azure Resource Manager model, kunt u deze niet koppelen aan de Azure Resource Manager-opslag accounts. Hier volgen enkele opties: 

- Implementeren via REST API.

    Wanneer u implementeert via Service Management REST API, kunt u de beperking omzeilen door een SAS-URL op te geven voor de Blob-opslag, die met zowel het klassieke als het Azure Resource Manager Storage-account wordt gebruikt. Lees [hier](/previous-versions/azure/reference/ee460813(v=azure.100))meer over de eigenschap ' PackageUrl '.

- Implementeren via [Azure Portal](https://portal.azure.com).

    Dit werkt vanaf de [Azure Portal](https://portal.azure.com) omdat de aanroep via een proxy/Shim wordt verzonden waarmee communicatie tussen Azure Resource Manager en klassieke resources mogelijk is. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Waarom moet ik een opslag account opgeven voor de implementatie van Azure Portal?

In de klassieke Portal is het pakket rechtstreeks naar de Management API-laag geüpload, waarna de API-laag het pakket tijdelijk in een intern opslag account plaatst.  Dit proces veroorzaakt prestatie-en schaalbaarheids problemen omdat de API-laag niet is ontworpen om een file upload-service te zijn.  In het Azure Portal (Resource Manager-implementatie model) hebben we de tijdelijke stap voor het eerst uploaden naar de API-laag overgeslagen, wat resulteert in snellere en betrouwbaardere implementaties.

Net als voor de kosten is het zeer klein en kunt u hetzelfde opslag account voor alle implementaties gebruiken. U kunt de [reken machine voor opslag kosten](https://azure.microsoft.com/pricing/calculator/#storage1) gebruiken om de kosten te bepalen voor het uploaden van het service pakket (CSPKG), de CSPKG te downloaden en vervolgens de CSPKG te verwijderen.
