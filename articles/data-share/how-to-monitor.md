---
title: De Azure-gegevens share bewaken
description: Meer informatie over het controleren van de status van de uitnodiging, het delen van abonnementen en de geschiedenis van moment opnamen in een Azure-gegevens share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92910471"
---
# <a name="monitor-azure-data-share"></a>Azure Data Share bewaken  

In dit artikel wordt uitgelegd hoe u uw gegevens shares kunt bewaken met behulp van de Azure-gegevens share. Als gegevens provider kunt u verschillende aspecten van uw relaties voor het delen van gegevens bewaken. Details over de vraag of uw gegevens gebruikers uw uitnodiging voor de gegevens share hebben geaccepteerd en of ze een share abonnement hebben gemaakt en dat ze uw gegevens kunnen gebruiken, zijn allemaal beschikbaar voor bewaking. 

Als gegevens verbruiker kunt u de moment opnamen bewaken die zijn geactiveerd in uw Azure-abonnement. 

## <a name="monitor-invitation-status"></a>Status van uitnodiging bewaken

Bekijk de status van uw uitnodigingen voor gegevens delen door te navigeren naar verzonden shares->-uitnodigingen. 

![Status van uitnodiging](./media/invitation-status.png "Status van uitnodiging") 

Er zijn drie statussen die uw uitnodiging kan bevinden:

* In behandeling: de ontvanger van het gegevens gedeelte heeft de uitnodiging nog niet geaccepteerd.
* Geaccepteerd: ontvanger van gegevens delen heeft de uitnodiging geaccepteerd.
* Afgewezen-ontvanger van gegevens delen heeft de uitnodiging afgewezen.

> [!IMPORTANT]
> Als u een uitnodiging verwijdert nadat deze al is geaccepteerd, is deze niet gelijk aan het intrekken van de toegang. Als u wilt voor komen dat toekomstige moment opnamen naar het opslag account van uw data-consumers worden gekopieerd, moet u de toegang intrekken via het tabblad *abonnementen delen* . 

## <a name="monitor-share-subscriptions"></a>Share abonnementen bewaken

Bekijk de status van uw abonnementen voor delen door te navigeren naar verzonden shares->-abonnementen. Dit geeft u details over actieve abonnementen die door uw gegevens gebruikers zijn gemaakt nadat u uw uitnodiging hebt geaccepteerd. U kunt toekomstige updates voor uw gegevens verbruiker stoppen door het abonnement delen te selecteren en *intrekken* te selecteren. 

## <a name="snapshot-history"></a>Momentopname geschiedenis 

Op het tabblad **geschiedenis** van een share kunt u zien wanneer gegevens worden gekopieerd van gegevens provider naar het gegevens archief van de gegevens consument. U kunt de frequentie, duur en status van elke moment opname bewaken. 

![De scherm opname toont de verzonden shares in de Azure Portal.](./media/sent-shares.png "Momentopname geschiedenis") 

U kunt meer informatie over de uitvoering van elke moment opname bekijken door te klikken op de begin datum van de uitvoering. Klik vervolgens op de status van elke gegevensset om de hoeveelheid overgedragen gegevens weer te geven, het aantal gekopieerde bestanden/records, de duur van de moment opname, het aantal gebruikte vCores en het fout bericht als er een is. 

Er worden Maxi maal 30 dagen aan de momentopname geschiedenis weer gegeven. Als u meer dan 30 dagen aan geschiedenis wilt besparen, kunt u gebruikmaken van de diagnostische instelling.

## <a name="diagnostic-setting"></a>Diagnostische instelling

U kunt de diagnostische instelling zo configureren dat logboek gegevens of-gebeurtenissen worden opgeslagen. Navigeer naar bewaking-> Diagnostische instellingen en selecteer **Diagnostische instelling toevoegen**. Selecteer de logboek gegevens of gebeurtenissen die u wilt, en waar u deze wilt opslaan of verzenden. 

![Scherm afbeelding toont de pagina Diagnostische instellingen in de Azure Portal.](./media/diagnostic-settings.png "Diagnostische instellingen") 

## <a name="next-steps"></a>Volgende stappen 

Meer informatie over de [terminologie van Azure data share](terminology.md)