---
title: Uitgebreide levenscyclusondersteuning voor Red Hat Enterprise Linux
description: Meer informatie over het toevoegen van ondersteuning voor Red Hat Enter prise uitgebreide levens cyclus op
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 703732725ae7215d3ff59ad92a4c171a86251c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677184"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Ondersteuning voor uitgebreide levenscyclus Red Hat Enterprise Linux (RHEL)
In dit artikel vindt u informatie over de uitgebreide levenscyclus ondersteuning voor Red Hat Enter prise-installatie kopieën:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 levenscyclus
Vanaf 30 november 2020 zal Red Hat Enterprise Linux 6 het einde van de onderhouds fase bereiken. De onderhouds fase wordt gevolgd door de verlengde levens fase. Als Red Hat Enterprise Linux 6 overgangen van de volledige/onderhouds fase, wordt het ten zeerste aanbevolen een upgrade uit te brengen naar Red Hat Enterprise Linux 7 of 8. Als klanten op Red Hat Enterprise Linux 6 moeten blijven, wordt het aanbevolen de invoeg toepassing Red Hat Enterprise Linux Extended Life Cycle Support (ELS) toe te voegen.

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Stappen om uitgebreide levenscyclus ondersteuning toe te voegen voor Vm's met betalen per gebruik op Marketplace
1. Vul het [beschik bare Els-formulier](https://aka.ms/els-form) in met uw contact gegevens en abonnements gegevens van de virtuele machines waarvoor u Els-ondersteuning wilt toevoegen. De gegevens voor het toevoegen van prijzen zijn ook beschikbaar in het formulier.
1. Azure Red Hat Enterprise Linux-team komt met een lijst met Vm's voor ELS-ondersteuning toe binnen 1-2 werk dagen. Controleer de lijst en reageer op het accepteren van de prijs opgave.
1. In azure Red Hat Enterprise Linux team worden de stappen gedeeld om het ELS-client pakket toe te voegen aan de virtuele machines. Volg de stappen in het e-mail bericht om software onderhoud te blijven ontvangen (oplossingen voor fouten en beveiliging) en ondersteuning voor Red Hat Enterprise Linux 6.

> [!Note]
> Deel de stappen voor het gebruik van RHEL ELS add with niet met iemand buiten uw organisatie. Neem contact op met AzureRedHatELS@microsoft.com om ondersteuning te krijgen of voor aanvullende vragen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Ik voer Red Hat Enterprise Linux 6 uit en kan op dit moment niet migreren naar een nieuwere versie. Welke opties heb ik?
* Ga verder met Red Hat Enterprise Linux 6 en koop de uitgebreide levenscyclus ondersteuning (ELS) Add-On opslag plaatsen om beperkte software onderhoud en technische ondersteuning te blijven ontvangen (Zie proces voor upgrade en prijs informatie hieronder).
* Migreer naar Red Hat Enterprise Linux 7 of 8 zo snel mogelijk.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Wat zijn de extra kosten voor het gebruik van Red Hat Enterprise Linux-invoeg toepassing voor de uitgebreide levenscyclus cyclus (ELS)?
De kosten met betrekking tot uitgebreide levenscyclus ondersteuning vindt u in het [formulier Els](https://aka.ms/els-form)

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>Ik heb een virtuele machine geïmplementeerd met behulp van aangepaste installatie kopie. Hoe kan ik uitgebreide ondersteuning voor de levens cyclus toevoegen aan deze virtuele machine?
U moet rechtstreeks contact opnemen met Red Hat en direct ondersteuning krijgen.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>Ik heb een virtuele machine geïmplementeerd met behulp van aangepaste installatie kopie. Kan ik deze VM converteren naar een PAYG-VM?
Nee, dat kan niet. De conversie wordt momenteel niet ondersteund in Azure.


## <a name="next-steps"></a>Volgende stappen

* Zie [Red Hat Enterprise Linux (RHEL)-installatie kopieën die beschikbaar zijn in azure](./redhat-imagelist.md)om de volledige lijst met RHEL-installatie kopieën in azure weer te geven.
* Zie voor meer informatie over de Azure Red Hat-update-infra [structuur voor Red Hat update voor on-demand RHEL-vm's in azure](./redhat-rhui.md).
* Voor meer informatie over de RHEL BYOS-aanbieding raadpleegt u [Red Hat Enterprise Linux uw Gold-installatie kopieën met uw eigen abonnement in azure](./byos.md).
* Zie [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata)voor meer informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL.