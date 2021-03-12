---
title: Windows-client installatie kopieën in azure gebruiken
description: De voor delen van Visual Studio-abonnementen gebruiken om Windows 7, Windows 8 of Windows 10 in azure te implementeren voor dev/test-scenario's
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 21038a8d1eabfcca21329c093b866607f0343070
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200007"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Windows-client in Azure gebruiken voor scenario's voor ontwikkelen/testen
U kunt Windows 7, Windows 8 of Windows 10 Enter prise (x64) gebruiken in azure voor ontwikkel-en test scenario's met de juiste Visual Studio (voorheen MSDN)-abonnement. 

Als u Windows 10 wilt uitvoeren in een productie omgeving, raadpleegt u [Windows 10 implementeren op Azure met multi tenant-hosting rechten](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Abonnements geschiktheid
Actieve Visual Studio-abonnees (personen die een licentie voor een Visual Studio-abonnement hebben aangeschaft) kunnen Windows-client installatie kopieën gebruiken voor ontwikkelings-en test doeleinden. Windows-client installatie kopieën kunnen worden gebruikt op uw eigen hardware of op virtuele machines van Azure.

Bepaalde Windows-client installatie kopieën zijn beschikbaar via Azure Marketplace. Visual Studio-abonnees binnen elk type aanbieding kunnen ook 64-bits Windows 7-, Windows 8-of Windows 10-installatie kopieën [voorbereiden](prepare-for-upload-vhd-image.md) en vervolgens [uploaden naar Azure](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>In aanmerking komende aanbiedingen en client installatie kopieën
De volgende tabel bevat informatie over de aanbieding-Id's die in aanmerking komen voor het implementeren van Windows-client installatie kopieën via de Azure Marketplace. De Windows-client installatie kopieën zijn alleen zichtbaar voor de volgende aanbiedingen. 

| Naam van aanbieding | Aanbiedings nummer | Beschik bare client installatie kopieën | 
|:--- |:---:|:---:|
| [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enter prise N (x64) <br> Windows 8,1 ENTER prise N (x64) <br> Windows 7 Enter prise N met SP1 (x64) |
| [Visual Studio Enterprise (MPN)-abonnees](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enter prise N (x64) <br> Windows 8,1 ENTER prise N (x64) <br> Windows 7 Enter prise N met SP1 (x64) |
| [Visual Studio Professional-abonnees](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enter prise N (x64) <br> Windows 8,1 ENTER prise N (x64) <br> Windows 7 Enter prise N met SP1 (x64) |
| [Visual Studio Test Professional-abonnees](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enter prise N (x64) <br> Windows 8,1 ENTER prise N (x64) <br> Windows 7 Enter prise N met SP1 (x64) |
| [Visual Studio Premium met MSDN (voor deel)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enter prise N (x64) <br> Windows 8,1 ENTER prise N (x64) <br> Windows 7 Enter prise N met SP1 (x64) |
| [Abonnees van Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enter prise N (x64) <br> Windows 8,1 ENTER prise N (x64) <br> Windows 7 Enter prise N met SP1 (x64) |
| [Visual Studio Enterprise (BizSpark)-abonnees](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enter prise N (x64) <br> Windows 8,1 ENTER prise N (x64) <br> Windows 7 Enter prise N met SP1 (x64) |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enter prise N (x64) <br> Windows 8,1 ENTER prise N (x64) <br> Windows 7 Enter prise N met SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Uw Azure-abonnement controleren
Als u uw aanbiedings-ID niet weet, kunt u deze verkrijgen via de Azure Portal.  
- In het venster *abonnementen* : ![ Details van de aanbiedings-ID van de Azure Portal](./media/client-images/offer-id-azure-portal.png) 
- U kunt ook op **facturering** klikken en vervolgens op uw abonnements-id klikken. De aanbiedings-ID wordt weer gegeven in het *facturerings* venster. 
- U kunt de aanbiedings-ID ook bekijken op het [tabblad abonnementen](https://account.windowsazure.com/Subscriptions) van de Azure-account portal: ![ Details van de aanbiedings-id van de Azure-account Portal](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Volgende stappen
U kunt nu uw Vm's implementeren met behulp van [Power shell](quick-create-powershell.md), [Resource Manager-sjablonen](ps-template.md)of [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
