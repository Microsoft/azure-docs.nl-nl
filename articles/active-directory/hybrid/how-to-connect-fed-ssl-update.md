---
title: Azure AD Connect-het TLS/SSL-certificaat voor een AD FS-farm bijwerken | Microsoft Docs
description: In dit document vindt u informatie over de stappen voor het bijwerken van het TLS/SSL-certificaat van een AD FS-farm met behulp van Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451b50e70b98849dfc4654566d09a5a961abe451
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279904"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Het TLS/SSL-certificaat voor een Active Directory Federation Services-farm (AD FS) bijwerken

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u Azure AD Connect kunt gebruiken om het TLS/SSL-certificaat voor een Active Directory Federation Services-farm (AD FS) bij te werken. U kunt het hulp programma Azure AD Connect gebruiken om het TLS/SSL-certificaat voor de AD FS Farm eenvoudig bij te werken, zelfs als de geselecteerde aanmeldings methode van de gebruiker niet is AD FS.

U kunt de gehele bewerking van het TLS/SSL-certificaat voor de AD FS-Farm voor alle Federatie-en Web Application proxy-servers (WAP) uitvoeren in drie eenvoudige stappen:

![Drie stappen](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Zie certificaten die worden [gebruikt door AD FS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc730660(v=ws.11))voor meer informatie over certificaten die worden gebruikt door AD FS.

## <a name="prerequisites"></a>Vereisten

* **AD FS Farm**: Zorg ervoor dat uw AD FS-farm Windows Server 2012 R2 of hoger is.
* **Azure AD Connect**: Controleer of de versie van Azure AD Connect 1.1.553.0 of hoger is. U gebruikt het taak **Update AD FS SSL-certificaat**.

![TLS-taak bijwerken](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Stap 1: Geef AD FS farm gegevens op

Azure AD Connect probeert automatisch informatie over de AD FS Farm te verkrijgen door:
1. Query's uitvoeren op de farm gegevens van AD FS (Windows Server 2016 of hoger).
2. Verwijzende informatie over eerdere uitvoeringen, die lokaal worden opgeslagen met Azure AD Connect.

U kunt de lijst met servers die worden weer gegeven, wijzigen door de servers toe te voegen of te verwijderen om de huidige configuratie van de AD FS Farm weer te geven. Zodra de server gegevens zijn verstrekt, wordt in Azure AD Connect de status van de verbinding en het huidige TLS/SSL-certificaat weer gegeven.

![AD FS-server gegevens](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Als de lijst een server bevat die niet langer deel uitmaakt van de AD FS Farm, klikt u op **verwijderen** om de server te verwijderen uit de lijst met servers in uw AD FS-farm.

![Offline server in lijst](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Het verwijderen van een server uit de lijst met servers voor een AD FS Farm in Azure AD Connect is een lokale bewerking en werkt de informatie bij voor de AD FS-farm die Azure AD Connect lokaal onderhoudt. Azure AD Connect wijzigt de configuratie op AD FS niet om de wijziging weer te geven.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Stap 2: een nieuw TLS/SSL-certificaat opgeven

Nadat u de informatie hebt bevestigd over AD FS-Farm servers, wordt Azure AD Connect gevraagd om het nieuwe TLS/SSL-certificaat. Geef een PFX-certificaat met wachtwoord beveiliging op om door te gaan met de installatie.

![TLS/SSL-certificaat](./media/how-to-connect-fed-ssl-update/certificate.png)

Nadat u het certificaat hebt opgegeven, wordt Azure AD Connect een aantal vereisten door lopen. Controleer het certificaat om er zeker van te zijn dat het certificaat juist is voor de AD FS Farm:

-   De onderwerpnaam/alternatieve onderwerpnaam voor het certificaat is hetzelfde als de naam van de Federation-service of het is een Joker certificaat.
-   Het certificaat is langer dan 30 dagen geldig.
-   De certificaat vertrouwens keten is geldig.
-   Het certificaat is beveiligd met een wachtwoord.

## <a name="step-3-select-servers-for-the-update"></a>Stap 3: servers selecteren voor de update

Selecteer in de volgende stap de servers waarop het TLS/SSL-certificaat moet worden bijgewerkt. Servers die offline zijn, kunnen niet worden geselecteerd voor de update.

![Servers selecteren die moeten worden bijgewerkt](./media/how-to-connect-fed-ssl-update/selectservers.png)

Nadat u de configuratie hebt voltooid, wordt in Azure AD Connect het bericht weer gegeven dat de status van de update aangeeft en biedt een optie om de AD FS-aanmelding te controleren.

![Configuratie voltooid](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Veelgestelde vragen

* **Wat moet de onderwerpnaam van het certificaat zijn voor het nieuwe AD FS TLS/SSL-certificaat?**

    Azure AD Connect controleert of de onderwerpnaam/alternatieve onderwerpnaam van het certificaat de naam van de Federation-service bevat. Als de naam van de Federation-service bijvoorbeeld fs.contoso.com is, moet de onderwerpnaam/alternatieve naam van het onderwerp fs.contoso.com zijn.  Joker tekens worden ook geaccepteerd.

* **Waarom wordt er opnieuw om referenties gevraagd op de pagina WAP-server?**

    Als de referenties die u opgeeft voor het maken van verbinding met AD FS servers, niet ook beschikken over de bevoegdheid om de WAP-servers te beheren, wordt Azure AD Connect gevraagd om referenties met beheerders bevoegdheden op de WAP-servers.

* **De server wordt weer gegeven als offline. Wat moet ik doen?**

    Azure AD Connect kan geen enkele bewerking uitvoeren als de server offline is. Als de server deel uitmaakt van de AD FS Farm, controleert u de verbinding met de server. Nadat u het probleem hebt opgelost, klikt u op het pictogram Vernieuwen om de status in de wizard bij te werken. Als de server eerder is opgenomen in de farm, maar nu niet meer bestaat, klikt u op **verwijderen** om deze te verwijderen uit de lijst met servers die Azure AD Connect onderhoudt. Als u de server verwijdert uit de lijst in Azure AD Connect wordt de AD FS configuratie zelf niet gewijzigd. Als u AD FS in Windows Server 2016 of hoger gebruikt, blijft de server in de configuratie-instellingen en wordt de volgende keer dat de taak wordt uitgevoerd opnieuw weer gegeven.

* **Kan ik een subset van mijn Farm servers bijwerken met het nieuwe TLS/SSL-certificaat?**

    Ja. U kunt het **SSL-certificaat** voor taak updates altijd opnieuw uitvoeren om de resterende servers bij te werken. Op de pagina **servers voor SSL-certificaat update selecteren** kunt u de lijst met servers op de **verval datum SSL** sorteren om eenvoudig toegang te krijgen tot de servers die nog niet zijn bijgewerkt.

* **Ik heb de server verwijderd uit de vorige uitvoering, maar wordt nog steeds weer gegeven als offline en wordt weer gegeven op de pagina AD FS servers. Waarom is er nog steeds een offline server, zelfs nadat ik deze heb verwijderd?**

    Als u de server uit de lijst in Azure AD Connect verwijdert, wordt deze niet verwijderd uit de AD FS configuratie. Azure AD Connect verwijst naar AD FS (Windows Server 2016 of hoger) voor informatie over de farm. Als de server nog steeds aanwezig is in de configuratie van AD FS, wordt deze weer gegeven in de lijst.  

## <a name="next-steps"></a>Volgende stappen

- [Azure AD Connect en federatie](how-to-connect-fed-whatis.md)
- [Active Directory Federation Services beheer en aanpassingen met Azure AD Connect](how-to-connect-fed-management.md)