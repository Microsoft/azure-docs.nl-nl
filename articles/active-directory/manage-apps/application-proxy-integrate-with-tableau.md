---
title: Azure Active Directory-toepassingsproxy en tableau | Microsoft Docs
description: Meer informatie over het gebruik van Azure Active Directory (Azure AD)-toepassings proxy om externe toegang te bieden voor uw tableau-implementatie.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6720a5ad963bc73e11ef7b46150e946521928c01
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258622"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory-toepassingsproxy en tableau 

Azure Active Directory-toepassingsproxy en tableau hebben een partner om ervoor te zorgen dat u eenvoudig een toepassings proxy kunt gebruiken om externe toegang te bieden voor uw tableau-implementatie. In dit artikel wordt uitgelegd hoe u dit scenario kunt configureren.  

## <a name="prerequisites"></a>Vereisten 

In het scenario in dit artikel wordt ervan uitgegaan dat u het volgende hebt:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) geconfigureerd. 

- Er is een [Application proxy-connector](application-proxy-add-on-premises-application.md) geïnstalleerd. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Toepassings proxy inschakelen voor tableau 

Toepassings proxy biedt ondersteuning voor de OAuth 2,0-toekennings stroom, die vereist is voor een goede werking van tableau. Dit betekent dat er geen speciale stappen meer nodig zijn om deze toepassing in te scha kelen, behalve de configuratie door de onderstaande publicatie stappen te volgen.


## <a name="publish-your-applications-in-azure"></a>Uw toepassingen publiceren in azure 

Als u tableau wilt publiceren, moet u een toepassing publiceren in azure Portal.

Voor:

- Gedetailleerde instructies voor stap 1-8, Zie [toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md). 
- Zie de tableau-documentatie voor informatie over het zoeken naar tableau-waarden voor de app-proxy velden.  

**Uw app publiceren**: 


1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een toepassings beheerder. 

2. Selecteer **Azure Active Directory > bedrijfs toepassingen**. 

3. Selecteer **toevoegen** boven aan de Blade. 

4. Selecteer **on-premises toepassing**. 

5. Vul de vereiste velden in met informatie over uw nieuwe app. Gebruik de volgende richt lijnen voor de instellingen: 

    - **Interne URL**: deze toepassing moet een interne URL hebben die de tableau-URL zelf is. Bijvoorbeeld `https://adventure-works.tableau.com`. 

    - **Methode voor verificatie vooraf**: Azure Active Directory (aanbevolen maar niet vereist). 

6. Selecteer **toevoegen** boven aan de Blade. Uw toepassing wordt toegevoegd en het menu snel starten wordt geopend. 

7. Selecteer in het menu snel starten de optie **een gebruiker toewijzen voor testen** en voeg ten minste één gebruiker toe aan de toepassing. Zorg ervoor dat dit test account toegang heeft tot de on-premises toepassing. 

8. Selecteer **toewijzen** om de gebruikers toewijzing test op te slaan. 

9. Beschrijving Selecteer op de pagina app-beheer de optie **eenmalige aanmelding**. Kies **geïntegreerde Windows-verificatie** in de vervolg keuzelijst en vul de vereiste velden in op basis van uw tableau-configuratie. Selecteer **Opslaan**. 

 

## <a name="testing"></a>Testen 

Uw toepassing is nu gereed om te testen. Open de externe URL die u hebt gebruikt voor het publiceren van tableau en meld u aan als een gebruiker die is toegewezen aan beide toepassingen.



## <a name="next-steps"></a>Volgende stappen

Zie [veilige externe toegang bieden voor on-premises toepassingen](application-proxy.md)voor meer informatie over Azure AD-toepassingsproxy.

