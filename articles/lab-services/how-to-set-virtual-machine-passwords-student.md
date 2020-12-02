---
title: Wacht woorden opnieuw instellen voor het leslokaal Lab-Vm's in Azure Lab Services | Microsoft Docs
description: Meer informatie over het opnieuw instellen van wacht woorden voor virtuele machines (Vm's) in Labs van Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1b0b13862ca4620da15606138c0a80adeac8056a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436807"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>Wacht woord instellen of opnieuw instellen voor virtuele machines in Labs (studenten)
In dit artikel wordt beschreven hoe studenten het wacht woord voor hun Vm's kunnen instellen of opnieuw instellen. 

## <a name="enable-resetting-of-passwords"></a>Het opnieuw instellen van wacht woorden inschakelen
Op het moment van het maken van het lab, kan de eigenaar van het lab het **gebruik van hetzelfde wacht woord voor alle virtuele machines** in-of uitschakelen. Als deze optie is ingeschakeld, kunnen studenten het wacht woord niet opnieuw instellen. Alle virtuele machines in de Labs hebben hetzelfde wacht woord dat is ingesteld door de docent. 

Als deze optie is uitgeschakeld, moeten gebruikers een wacht woord instellen wanneer er voor de eerste keer verbinding wordt gemaakt met de virtuele machine. Studenten kunnen het wacht woord ook later op elk gewenst moment opnieuw instellen, zoals wordt weer gegeven in de laatste sectie van dit artikel. 

## <a name="reset-password-for-the-first-time"></a>Wacht woord voor de eerste keer opnieuw instellen
Als de optie **hetzelfde wacht woord voor alle virtuele machines gebruiken** is uitgeschakeld, wanneer gebruikers (studenten) de knop **verbinden** selecteren op de Lab-tegel op de pagina **mijn virtuele machines** , ziet de gebruiker het volgende dialoog venster om het wacht woord voor de virtuele machine in te stellen: 

![Het wacht woord voor de student opnieuw instellen](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Wacht woord later opnieuw instellen
Student kunt u het wacht woord ook instellen door te klikken op het overloop menu (**Verticaal drie punten**) op de tegel Lab en vervolgens **wacht woord opnieuw instellen** te selecteren. 

![Wacht woord later opnieuw instellen](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel: het [gebruik van studenten configureren](how-to-configure-student-usage.md)voor meer informatie over andere gebruiks opties voor studenten die een Lab-eigenaar kan configureren.
