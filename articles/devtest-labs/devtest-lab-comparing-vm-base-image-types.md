---
title: Aangepaste afbeeldingen en formules vergelijken in DevTest Labs | Microsoft Docs
description: Meer informatie over de verschillen tussen aangepaste installatie kopieën en formules als VM-basis, zodat u kunt bepalen welke methode het beste past bij uw omgeving.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: df7a8e6209f8033eb5a29c65079e9c2f4cbbe544
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87287532"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Aangepaste afbeeldingen en formules in DevTest Labs vergelijken
Zowel [aangepaste installatie kopieën](devtest-lab-create-template.md) als [formules](devtest-lab-manage-formulas.md) kunnen worden gebruikt als basis voor het [maken van nieuwe vm's](devtest-lab-add-vm.md). Het belangrijkste onderscheid tussen aangepaste afbeeldingen en formules is echter dat een aangepaste installatie kopie een afbeelding is op basis van een VHD, terwijl een formule een afbeelding is op basis van een VHD *naast* vooraf geconfigureerde instellingen, zoals de VM-grootte, het virtuele netwerk, het subnet en de artefacten. Deze vooraf geconfigureerde instellingen worden ingesteld met standaard waarden die kunnen worden overschreven op het moment dat de VM wordt gemaakt. In dit artikel worden enkele voor delen (voor-en nadelen) beschreven voor het gebruik van aangepaste installatie kopieën en het gebruik van formules.

## <a name="custom-image-pros-and-cons"></a>Voor beelden van aangepaste afbeeldingen en nadelen
Aangepaste installatie kopieën bieden een statische, onveranderlijke manier om Vm's te maken op basis van een gewenste omgeving. 

**Voordelen**

* Het inrichten van een virtuele machine vanuit een aangepaste installatie kopie is snel omdat er niets verandert nadat de virtuele machine van de installatie kopie is gemaakt. Met andere woorden, er zijn geen instellingen die kunnen worden toegepast als de aangepaste installatie kopie alleen een installatie kopie zonder instellingen is. 
* Vm's die zijn gemaakt op basis van één aangepaste installatie kopie, zijn identiek.

**Nadelen**

* Als u een aspect van de aangepaste installatie kopie moet bijwerken, moet de afbeelding opnieuw worden gemaakt.  

## <a name="formula-pros-and-cons"></a>Voor-en nadelen van formules
Formules bieden een dynamische manier om Vm's te maken op basis van de gewenste configuratie/instellingen.

**Voordelen**

* Wijzigingen in de omgeving kunnen worden vastgelegd via artefacten. Als u bijvoorbeeld een VM wilt installeren met de meest recente bits van uw release pijplijn of als u de meest recente code van uw opslag plaats wilt inschrijven, kunt u gewoon een artefact opgeven dat de meest recente bits implementeert of de meest recente code in de formule aanmeldt samen met een doel basis installatie kopie. Wanneer deze formule wordt gebruikt voor het maken van Vm's, worden de meest recente bits/code geïmplementeerd/aangemeld bij de VM. 
* Formules kunnen standaard instellingen definiëren die aangepaste installatie kopieën niet kunnen bieden, zoals VM-grootten en instellingen voor virtuele netwerken. 
* De instellingen die zijn opgeslagen in een formule worden weer gegeven als standaard waarden, maar kunnen worden gewijzigd wanneer de virtuele machine wordt gemaakt. 

**Nadelen**

* Het maken van een VM op basis van een formule kan meer tijd in beslag nemen dan het maken van een virtuele machine vanuit een aangepaste installatie kopie.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Gerelateerde blog berichten
* [Aangepaste afbeeldingen of formules?](./devtest-lab-faq.md#blog-post)

## <a name="next-steps"></a>Volgende stappen
- [Veelgestelde vragen over DevTest Labs](devtest-lab-faq.md)
