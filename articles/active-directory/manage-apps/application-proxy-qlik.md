---
title: Azure AD-app proxy en Qlik Sense | Microsoft Docs
description: Schakel toepassings proxy in het Azure Portal in en installeer de connectors voor de omgekeerde proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a03f3c57d3fbc7efe20055343eab3db33aa8194
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259081"
---
# <a name="application-proxy-and-qlik-sense"></a>Toepassings proxy en Qlik Sense 
Azure Active Directory-toepassingsproxy en Qlik Sense kunnen samen worden gebruikt om ervoor te zorgen dat u eenvoudig een toepassings proxy kunt gebruiken om externe toegang te bieden voor uw Qlik Sense-implementatie.  

## <a name="prerequisites"></a>Vereisten 
In de rest van dit scenario wordt ervan uitgegaan dat u het volgende hebt gedaan:
 
- [Qlik Sense](https://community.qlik.com/docs/DOC-19822)is geconfigureerd. 
- [Een toepassings proxy connector geïnstalleerd](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Uw toepassingen publiceren in azure 
Als u QlikSense wilt publiceren, moet u twee toepassingen publiceren in Azure.  

### <a name="application-1"></a>#1 van toepassing: 
Volg deze stappen om uw app te publiceren. Zie [toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md)voor een meer gedetailleerd overzicht van de stappen 1-8. 


1. Meld u aan bij de Azure Portal als globale beheerder. 
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**. 
3. Selecteer **toevoegen** boven aan de Blade. 
4. Selecteer **on-premises toepassing**. 
5. Vul de vereiste velden in met informatie over uw nieuwe app. Gebruik de volgende richt lijnen voor de instellingen: 
   - **Interne URL**: deze toepassing moet een interne URL hebben die de QLIKSENSE-URL zelf is. Bijvoorbeeld: **https&#58;//demo.qlikemm.com:4244** 
   - **Methode voor verificatie vooraf**: Azure Active Directory (aanbevolen maar niet vereist) 
1. Selecteer **toevoegen** onder aan de Blade. Uw toepassing wordt toegevoegd en het menu snel starten wordt geopend. 
2. Selecteer in het menu snel starten de optie **een gebruiker toewijzen voor testen** en voeg ten minste één gebruiker toe aan de toepassing. Zorg ervoor dat dit test account toegang heeft tot de on-premises toepassing. 
3. Selecteer **toewijzen** om de gebruikers toewijzing test op te slaan. 
4. Beschrijving Selecteer op de Blade app-beheer de optie eenmalige aanmelding. Kies **Kerberos-beperkte delegering** in de vervolg keuzelijst en vul de vereiste velden in op basis van uw Qlik-configuratie. Selecteer **Opslaan**. 

### <a name="application-2"></a>#2 van toepassing: 
Volg dezelfde stappen als voor toepassings #1, met de volgende uitzonde ringen: 

**Stap #5**: de interne URL moet nu de QlikSense URL zijn met de verificatie poort die door de toepassing wordt gebruikt. De standaard waarde is **4244** voor HTTPS en **4248** voor http voor QlikSense releases van vóór april 2018. De standaard waarde voor QlikSense-releases na april 2018 is **443** voor HTTPS en **80** voor http.  Bijvoorbeeld: **https&#58;//demo.qlik.com:4244**</br></br>
**Stap #10:** EENMALIGe aanmelding instellen en de **eenmalige aanmelding uitgeschakeld** laten
 
 
## <a name="testing"></a>Testen 
Uw toepassing is nu gereed om te testen. Open de externe URL die u hebt gebruikt voor het publiceren van QlikSense in toepassings #1 en meld u aan als een gebruiker die is toegewezen aan beide toepassingen.  

## <a name="additional-references"></a>Aanvullende naslaginformatie
Raadpleeg de volgende artikelen in de Qlik-Community voor meer informatie over het publiceren van Qlik Sense met toepassings proxy: 
- [Azure AD met geïntegreerde Windows-verificatie met behulp van een Kerberos-beperkte delegering met Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense-integratie met Azure AD-toepassingsproxy](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingen publiceren met toepassingsproxy (Engelstalig artikel)](application-proxy-add-on-premises-application.md)
- [Werken met Application proxy-connectors](application-proxy-connector-groups.md)

