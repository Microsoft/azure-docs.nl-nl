---
title: Azure Data Share-terminologie
description: Meer informatie over algemene termen die worden gebruikt voor het beschrijven van resources die worden gebruikt in de Azure-gegevens share (gegevens provider, gegevens verbruiker, gegevens share, share abonnement, moment opname, uitnodiging, ontvanger)
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "73468552"
---
# <a name="azure-data-share-concepts"></a>Azure Data Share-concepten 

Azure data share introduceert een aantal nieuwe terminologie met betrekking tot het delen van gegevens. In dit artikel worden enkele veelgebruikte termen beschreven die in de service kunnen worden gebruikt. 

## <a name="data-provider"></a>Gegevens provider

Een gegevens provider is de organisatie die gegevens deelt met hun consumenten. Doorgaans kan de gegevens provider een eigenaar of een curator van de gegevens zijn. Gegevens providers willen gegevens van verschillende typen delen. Enkele voor beelden van gegevens die een gegevens provider kan delen, zijn onbewerkte gegevens, zoals een verkoop punt of een time series-gegevens. Het is ook mogelijk dat een gegevens provider vooraf verwerkte, gecuratore gegevens die al analyse en inzichten bevatten delen. 

## <a name="data-consumer"></a>Gegevens verbruiker 

Een gegevens consument is de organisatie die gegevens van een gegevens provider ontvangt. De gegevens verbruiker kan de gedeelde gegevens ook samen voegen met hun eigen gegevens om inzichten te verkrijgen. In sommige gevallen kan de gegevens verbruiker gegevens ontvangen die al zijn verwerkt. 

## <a name="data-share"></a>Data Share

Een gegevensshare is een groep gegevenssets die worden gedeeld als één entiteit. Gegevenssets kunnen afkomstig zijn uit een aantal Azure-gegevensbronnen die worden ondersteund door Azure Data Share. Op dit moment ondersteunt Azure Data Share Azure Blob Storage en Azure Data Lake Store. 

## <a name="share-subscription"></a>Abonnement delen 

Er wordt een share abonnement gemaakt wanneer een gegevens verbruiker een uitnodiging voor gegevens delen van een gegevens provider accepteert. Gegevens providers kunnen actieve share-abonnementen weer geven door te navigeren naar **verzonden shares** in hun Azure-gegevens share-account en **abonnementen delen** te selecteren.

Een gegevens verbruiker kan controleren of ze een actief share-abonnement hebben door te navigeren naar **ontvangen shares** en de status van hun ontvangen shares te bekijken. 

## <a name="snapshot"></a>Momentopname

Een moment opname kan worden gemaakt door een gegevens verbruiker wanneer ze een uitnodiging voor een gegevens share accepteren. Wanneer ze een uitnodiging accepteren, kunnen ze een volledige moment opname activeren van de gegevens die met hen zijn gedeeld. De moment opname is een kopie van de gegevens op het moment dat de gegevens verbruiker de moment opname heeft gegenereerd. 

Er zijn twee typen moment opnamen: volledig en incrementeel. Een volledige moment opname bevat alle gegevens in de gegevens share. Een incrementele moment opname bevat alle gegevens die zijn bijgewerkt/toegevoegd sinds de laatste moment opname is geactiveerd. 

## <a name="snapshot-settings-in-azure-data-share"></a>Momentopname instellingen in azure data share
 
Een gegevens provider kan een moment opname-instelling voor een gegevens share inschakelen. Met deze instelling kunnen gegevens gebruikers incrementele updates ontvangen wanneer deze zich voordoen. Deze instelling moet worden ingeschakeld als de gegevens provider wil dat de gegevens van hun gebruikers updates ontvangen van gegevens die zijn gedeeld. 

Als een gegevens provider deze instelling inschakelt, kan een interval voor een terugkeer patroon worden geselecteerd. Het interval voor het terugkeer patroon kan elk uur of dagelijks zijn. 

Een gegevens verbruiker heeft de mogelijkheid om zich aan te melden bij dit momentopname schema voor het ontvangen van incrementele updates, waaronder alle gegevens die zijn gewijzigd sinds de eerste keer een nieuwe moment opname heeft gegenereerd. 

## <a name="invitation"></a>Nodig

Een gegevens provider kan meerdere ontvangers uitnodigen voor hun gegevens share. Ze kunnen dit doen door ontvangers toe te voegen aan de gegevens share. Uitnodigingen kunnen ook worden toegevoegd nadat een gegevens share is gemaakt. 

Een gegevens provider kan een uitnodiging verwijderen nadat deze is verzonden als deze nog niet is geaccepteerd. Als de gegevens provider een uitnodiging verwijdert en deze nog niet is geaccepteerd, kan de gebruiker de gegevens niet accepteren. 

Uitnodigingen kunnen Maxi maal vijf keer per dag worden verzonden. 

## <a name="recipient"></a>Ontvanger

Een ontvanger is iemand die een uitnodiging voor een gegevens share ontvangt. Normaal gesp roken voegt een gegevens provider ontvangers toe aan de gegevens share die ze maken. Zodra de ontvanger van een uitnodiging de uitnodiging heeft geaccepteerd, worden deze een gegevens consument.  

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md).
