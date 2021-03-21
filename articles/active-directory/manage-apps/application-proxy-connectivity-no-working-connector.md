---
title: Er is geen Working connector-groep gevonden voor een toepassings proxy-app
description: Problemen oplossen die kunnen optreden wanneer er geen werkende connector in een connector groep voor uw toepassing is met de Azure-AD-toepassingsproxy
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cec028dec2bf2e6295f59aae9c2e243da2209827
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257031"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Er is geen Working connector-groep gevonden voor een toepassings proxy toepassing

Dit artikel helpt u bij het oplossen van veelvoorkomende problemen die zich voordoen wanneer er geen connector wordt gedetecteerd voor een toepassings proxy toepassing die is geïntegreerd met Azure Active Directory.

## <a name="overview-of-steps"></a>Overzicht van stappen
Als er geen werkende connector in een connector groep voor uw toepassing is, zijn er een aantal manieren om het probleem op te lossen:

-   Als u geen connectors in de groep hebt, kunt u het volgende doen:

    -   Down load een nieuwe connector aan de rechter kant van de on-premises server en wijs deze toe aan deze groep

    -   Een actieve connector verplaatsen naar de groep

-   Als u geen actieve connectors in de groep hebt, kunt u het volgende doen:

    -   De reden voor het inactief zijn van uw connector identificeren en oplossen

    -   Een actieve connector verplaatsen naar de groep

Om het probleem op te lossen, opent u het menu toepassings proxy in uw toepassing en bekijkt u het waarschuwings bericht groep connector. Als er geen connectors in de groep zijn, geeft het waarschuwings bericht aan dat de groep ten minste één connector nodig heeft. Als u geen actieve connectors hebt, wordt in het waarschuwings bericht uitgelegd dat. Het is gebruikelijk dat er niet-actieve connectors zijn. 

   ![Selectie van connector groep in Azure Portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Zie de bijbehorende sectie hieronder voor meer informatie over elk van deze opties. In de instructies wordt ervan uitgegaan dat u begint vanaf de pagina voor het beheren van connectors. Als u het bovenstaande fout bericht bekijkt, kunt u naar deze pagina gaan door te klikken op het waarschuwings bericht. U kunt de pagina ook openen door naar **Azure Active Directory** te gaan, op **bedrijfs toepassingen** en vervolgens op **toepassings proxy** te klikken.

   ![Groeps beheer voor connectors in Azure Portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Een nieuwe connector downloaden

Als u een nieuwe connector wilt downloaden, gebruikt u de knop ' connector downloaden ' boven aan de pagina.

Installeer de connector op een computer met een rechtstreekse gezichts lijn voor de back-end-toepassing. Normaal gesp roken wordt de connector geïnstalleerd op dezelfde server als de toepassing. Na het downloaden moet de connector in dit menu worden weer gegeven. Klik op de connector en gebruik de vervolg keuzelijst connector groep om te controleren of deze bij de juiste groep hoort. Sla de wijziging op.

   ![De connector downloaden van de Azure Portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Een actieve connector verplaatsen

Als u een actieve connector hebt die deel moet uitmaken van de groep en een regel voor de detectie van de doel-back-end-toepassing heeft, kunt u de connector verplaatsen naar de toegewezen groep. Als u dit wilt doen, klikt u op de connector. In het veld connector groep gebruikt u de vervolg keuzelijst om de juiste groep te selecteren en klikt u op opslaan.

## <a name="resolve-an-inactive-connector"></a>Een inactieve connector oplossen

Als de enige connectors in de groep inactief zijn, zijn ze waarschijnlijk op een computer waarop niet alle benodigde poorten zijn gedeblokkeerd.

Zie het document over het oplossen van problemen met poorten voor meer informatie over het onderzoeken van dit probleem.

## <a name="next-steps"></a>Volgende stappen
[Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)


