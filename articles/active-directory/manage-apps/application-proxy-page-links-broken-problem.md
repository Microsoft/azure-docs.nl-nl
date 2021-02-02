---
title: Koppelingen op de pagina werken niet voor een toepassings proxy toepassing
description: Problemen oplossen met verbroken koppelingen in toepassings proxy toepassingen die u hebt geïntegreerd met Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a8ecb908063a40da32c1397fd4249811f9c2712
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254611"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Koppelingen op de pagina werken niet voor een toepassings proxy toepassing

Dit artikel helpt u bij het oplossen van problemen met koppelingen op uw Azure Active Directory-toepassingsproxy-toepassing niet naar behoren werkt.

## <a name="overview"></a>Overzicht 
Nadat een toepassings proxy-app is gepubliceerd, zijn de enige koppelingen die standaard in de toepassing werken, koppelingen naar bestemmingen die zijn opgenomen in de gepubliceerde basis-URL. De koppelingen in de toepassingen werken niet. de interne URL voor de toepassing bevat waarschijnlijk niet alle bestemmingen van koppelingen binnen de toepassing.

**Waarom gebeurt dit?** Wanneer u op een koppeling in een toepassing klikt, probeert toepassings proxy de URL om te zetten als een interne URL binnen dezelfde toepassing of als een extern beschik bare URL. Als de koppeling verwijst naar een interne URL die zich niet in dezelfde toepassing bevindt, hoort deze niet bij een van deze buckets en resulteert dit in een fout die niet kan worden gevonden.

## <a name="ways-you-can-resolve-broken-links"></a>Manieren om verbroken koppelingen op te lossen

Er zijn drie manieren om dit probleem op te lossen. De onderstaande opties worden weer gegeven in toenemende complexiteit.

1.  Zorg ervoor dat de interne URL een hoofdmap is die alle relevante koppelingen voor de toepassing bevat. Hierdoor kunnen alle koppelingen worden omgezet als inhoud die binnen dezelfde toepassing wordt gepubliceerd.

    Als u de interne URL wijzigt, maar de landings pagina voor gebruikers niet wilt wijzigen, wijzigt u de URL van de start pagina in de eerder gepubliceerde interne URL. U kunt dit doen door te gaan naar ' Azure Active Directory '- &gt; app-registraties: &gt; Selecteer de toepassing- &gt; branding. In de sectie huis stijl ziet u het veld "URL van start pagina", die u kunt aanpassen aan de gewenste landings pagina. Als u nog steeds de verouderde App-registraties ervaring gebruikt, wordt op het tabblad Eigenschappen de informatie ' start pagina-URL ' weer gegeven. 
    
    > [!IMPORTANT]
    > Als u de bovenstaande wijzigingen wilt aanbrengen, moet u rechten hebben om toepassings objecten in azure AD te wijzigen. De gebruiker moet de rol van [toepassings beheerder](../roles/delegate-app-roles.md#assign-built-in-application-admin-roles) toewijzen die toepassings Modificaion in azure AD verleent aan de gebruiker.
    >

2.  Als uw toepassingen FQDN-namen (FULLy Qualified Domain names) gebruiken, moet u [aangepaste domeinen](application-proxy-configure-custom-domain.md) gebruiken om uw toepassingen te publiceren. Met deze functie kan dezelfde URL zowel intern als extern worden gebruikt.

    Deze optie zorgt ervoor dat de koppelingen in uw toepassing extern toegankelijk zijn via een toepassings proxy, omdat de koppelingen in de toepassing naar interne Url's ook extern worden herkend. Alle koppelingen moeten nog steeds deel uitmaken van een gepubliceerde toepassing. Met deze optie hoeven de koppelingen echter niet tot dezelfde toepassing te behoren en kunnen ze deel uitmaken van meerdere toepassingen.

3.  Als geen van deze opties haalbaar zijn, zijn er meerdere opties voor het inschakelen van de vertaling van inline-koppelingen. Deze opties zijn onder andere het Intune Managed Browser, mijn apps-extensie of het gebruik van de instelling voor de koppelings conversie in uw toepassing. Zie voor meer informatie over elk van deze opties en over hoe u deze kunt inschakelen de [omleiding van hardcoded koppelingen voor apps die zijn gepubliceerd met Azure AD-toepassingsproxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Volgende stappen
[Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)

