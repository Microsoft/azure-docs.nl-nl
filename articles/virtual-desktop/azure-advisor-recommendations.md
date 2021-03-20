---
title: Azure Advisor Windows virtueel bureau blad-walkthrough-Azure
description: Azure Advisor aanbevelingen voor Windows virtueel bureau blad oplossen.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 760e0212d2d863e6b869c23c2e523a0e056a28ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90033027"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Azure Advisor aanbevelingen oplossen

In dit artikel wordt beschreven hoe u aanbevelingen kunt oplossen die worden weer gegeven in Azure Advisor voor virtueel bureau blad van Windows.

## <a name="no-validation-environment-enabled"></a>Geen validatie omgeving ingeschakeld

>[!div class="mx-imgBorder"]
>![Een scherm opname van de Azure Advisor pagina voor operationele uitmuntendheid. De aanbeveling ' geen validatie omgeving ingeschakeld ' is rood gemarkeerd.](media/no-validation-environment.png)

Deze aanbeveling wordt weer gegeven onder operationele uitmuntendheid. In de aanbeveling wordt ook een waarschuwings bericht weer gegeven dat er als volgt uitziet:

' U hebt geen validatie omgeving ingeschakeld in dit abonnement. Wanneer u uw hostgroepen hebt gemaakt, hebt u **Nee** geselecteerd voor ' validatie omgeving ' op het tabblad Eigenschappen. Zorg ervoor dat u ten minste één hostgroep hebt met een validatie omgeving waarin u kunt testen op mogelijke problemen om ervoor te zorgen dat bedrijfs continuïteit wordt gegarandeerd door de implementaties van Windows Virtual Desktop service.

U kunt dit waarschuwings bericht laten verdwijnen door een validatie omgeving in een van uw hostgroepen in te scha kelen.

Een validatie omgeving inschakelen:

1. Ga naar de start pagina van Azure Portal en selecteer de hostgroep die u wilt wijzigen.

2. Selecteer vervolgens de hostgroep die u wilt wijzigen van een productie omgeving naar een validatie omgeving.

3. Selecteer **Eigenschappen** in de linkerkolom van de hostgroep. Schuif vervolgens omlaag totdat u "validatie omgeving" ziet. Selecteer **Ja** en selecteer vervolgens **Toep assen**.

>[!div class="mx-imgBorder"]
>![Een scherm opname van het eigenschappen menu. "Validatie omgeving" is rood gemarkeerd en de "ja" bellen is geselecteerd.](media/validation-yes.png)

Deze wijzigingen zorgen ervoor dat de waarschuwing niet onmiddellijk wordt verwijderd, maar moet uiteindelijk worden verwijderd. Azure Advisor updates twee keer per dag. Tot slot kunt u de aanbeveling hand matig uitstellen of sluiten. We raden u aan de aanbeveling zelf weg te nemen. Op die manier kan Azure Advisor u laten weten of het over eventuele problemen beschikt wanneer de instellingen veranderen.

## <a name="not-enough-production-non-validation-environments-enabled"></a>Er zijn niet voldoende productie omgevingen (niet-validatie) ingeschakeld

Deze aanbeveling wordt weer gegeven onder operationele uitmuntendheid.

Voor deze aanbeveling wordt het waarschuwings bericht om een van de volgende redenen weer gegeven:

- Uw validatie omgeving bevat te veel hostgroepen.
- U hebt geen productie-hostgroepen.

We raden aan dat gebruikers minder dan de helft van hun hostgroepen in een validatie omgeving hebben.

U kunt deze waarschuwing als volgt oplossen:

1. Ga naar de start pagina van Azure Portal.

2. Selecteer de hostgroepen die u wilt wijzigen van validatie naar productie.

3. Selecteer in uw hostgroep het tabblad **Eigenschappen** in de kolom aan de rechter kant van het scherm. Schuif vervolgens omlaag totdat u "validatie omgeving" ziet. Selecteer **Nee** en selecteer vervolgens **Toep assen**.

>[!div class="mx-imgBorder"]
>![Een scherm opname van het eigenschappen menu. "Validatie omgeving" is gemarkeerd in rood en de bellen "nee" is geselecteerd.](media/validation-no.png)

Deze wijzigingen zorgen ervoor dat de waarschuwing niet onmiddellijk wordt verwijderd, maar moet uiteindelijk worden verwijderd. Azure Advisor updates twee keer per dag. Tot slot kunt u de aanbeveling hand matig uitstellen of sluiten. We raden u aan de aanbeveling zelf weg te nemen. Op die manier kan Azure Advisor u laten weten of het over eventuele problemen beschikt wanneer de instellingen veranderen.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>' Er zijn onvoldoende koppelingen voor het implementeren van uw VM ' gedeblokkeerd '

Deze aanbeveling wordt weer gegeven onder operationele uitmuntendheid.

U moet specifieke Url's deblokkeren om ervoor te zorgen dat uw virtuele machine (VM) goed werkt. U ziet de lijst in de [lijst Veilige URL](safe-url-list.md). Als de Url's niet worden gedeblokkeerd, werkt de virtuele machine niet goed.

Om deze aanbeveling op te lossen, moet u ervoor zorgen dat alle Url's in de [lijst met veilige url's](safe-url-list.md)worden gedeblokkeerd. U kunt ook service tags of FQDN-Tags gebruiken om de deblokkerende Url's te blok keren.

## <a name="propose-new-recommendations"></a>Nieuwe aanbevelingen Voorst Ellen

U kunt ons helpen bij het verbeteren van Azure Advisor door ideeën voor aanbevelingen te verzenden. Uw aanbeveling kan een andere gebruiker van een moeilijkere plek helpen. Als u een suggestie wilt indienen, gaat u naar het [UserVoice-forum](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) en vult u het inzendings formulier in. Wanneer u het formulier invult, moet u ervoor zorgen dat het zo veel mogelijk details geeft.

## <a name="next-steps"></a>Volgende stappen

Als u meer gedetailleerde hand leidingen wilt over het oplossen van veelvoorkomende problemen, raadpleegt u [probleemoplossings overzicht, feedback en ondersteuning voor virtueel bureau blad van Windows](troubleshoot-set-up-overview.md).
