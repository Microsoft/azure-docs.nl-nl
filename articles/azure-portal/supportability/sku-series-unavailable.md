---
title: De regio-of SKU-serie is niet beschikbaar
description: Sommige SKU-reeksen zijn niet beschikbaar voor het geselecteerde abonnement voor deze regio. hiervoor is mogelijk een ondersteunings aanvraag voor abonnements beheer vereist.
author: stevendotwang
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 2bbe1dff6bbf8fed481300db95482c46b0e8a7b7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745278"
---
# <a name="region-or-sku-unavailable"></a>Regio of SKU is niet beschikbaar

In dit artikel wordt beschreven hoe u het probleem oplost van een Azure-abonnement dat geen toegang heeft tot een regio of een VM-SKU.

## <a name="symptoms"></a>Symptomen

Wanneer u een virtuele machine implementeert, wordt een van de volgende fout berichten weer gegeven:

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

Bij het kopen van gereserveerde exemplaren van virtuele machines, wordt een van de volgende fout berichten weer gegeven:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Bij het maken van een ondersteunings aanvraag om het quotum voor reken kernen te verhogen, is een regio of SKU-familie niet beschikbaar voor selectie.

## <a name="solution"></a>Oplossing

We raden u aan om een alternatieve regio of SKU te beschouwen die voldoet aan de behoeften van uw bedrijf.

Als u geen geschikte regio of SKU kunt vinden, maakt u een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) voor **abonnements beheer** aan de hand van de volgende stappen:

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) de optie **Help en ondersteuning**. Selecteer vervolgens **nieuwe ondersteunings aanvraag**.

1. In de **basis beginselen**, voor het **type probleem**, selecteert u **abonnements beheer**.

1. Selecteer een **abonnement** en voer een korte beschrijving in **samen vatting** in.

   ![Tabblad basis van nieuwe ondersteunings aanvraag](./media/SKU-series-unavailable/support-request-basics.png)

1. Voor **probleem type** kiest **u probleem type selecteren**.

1. Kies voor **type probleem selecteren** een optie, bijvoorbeeld, **geen toegang tot mijn abonnement of resource**  >  **mijn probleem niet wordt vermeld**. Selecteer **Opslaan**.

   ![Geef een probleem op voor de aanvraag](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Selecteer **volgende: oplossingen** om mogelijke oplossingen te verkennen. Selecteer indien nodig **volgende: Details** om door te gaan.

1. Voer aanvullende informatie in, samen met uw contact gegevens.

1. Selecteer **Controleren + maken**. Nadat u uw gegevens hebt gecontroleerd, selecteert u **maken** om de aanvraag te maken.

## <a name="send-us-your-suggestions"></a>Stuur ons uw suggesties

We zijn altijd te openen voor feedback en suggesties. Stuur ons uw [suggesties](https://feedback.azure.com/forums/266794-support-feedback). Daarnaast kunt u aan de slag met ons op [Twitter](https://twitter.com/azuresupport) of op de [pagina micro soft Q&een vraag](/answers/products/azure).

## <a name="learn-more"></a>Meer informatie

[Veelgestelde vragen over ondersteuning voor Azure](https://azure.microsoft.com/support/faq)