---
title: Lokale beheerders beheren op apparaten die zijn toegevoegd aan Azure AD
description: Meer informatie over het toewijzen van Azure-rollen aan de lokale groep Administrators van een Windows-apparaat.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfd7b5ac981fcb87d0fc929d944205dec9432b74
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575819"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>De lokale groep Administrators beheren op apparaten die zijn toegevoegd aan Azure AD

Als u een Windows-apparaat wilt beheren, moet u lid zijn van de lokale groep Administrators. Als onderdeel van het deelname proces van Azure Active Directory (Azure AD), wordt het lidmaatschap van deze groep door Azure AD bijgewerkt op een apparaat. U kunt de update van het lidmaatschap aanpassen om te voldoen aan uw bedrijfs vereisten. Een update van het lidmaatschap is bijvoorbeeld handig als u wilt dat uw helpdesk medewerkers taken kunnen uitvoeren waarvoor beheerders rechten op een apparaat zijn vereist.

In dit artikel wordt uitgelegd hoe de update van het lokale beheerders lidmaatschap werkt en hoe u deze kunt aanpassen tijdens een Azure AD-deelname. De inhoud van dit artikel is niet van toepassing op een **hybride Azure AD gekoppelde** apparaten.

## <a name="how-it-works"></a>Hoe werkt het?

Wanneer u een Windows-apparaat met Azure AD verbindt met een Azure AD-deelname, voegt Azure AD de volgende beveiligings-principals toe aan de lokale groep Administrators op het apparaat:

- De rol van de globale beheerder van Azure AD
- De rol van Azure AD-Apparaatbeheer 
- De gebruiker die de Azure AD-deelname uitvoert   

Door Azure AD-rollen toe te voegen aan de lokale groep Administrators, kunt u de gebruikers die een apparaat op elk gewenst moment in azure AD kunnen beheren, zonder dat u iets hoeft te wijzigen op het apparaat. Op dit moment kunt u geen groepen aan een beheerdersrol toewijzen.
Azure AD voegt ook de rol van Azure AD-apparaat beheerder toe aan de lokale groep Administrators om het principe van minimale bevoegdheden (PoLP) te ondersteunen. Naast de globale beheerders kunt u ook gebruikers inschakelen waaraan *alleen* de rol van apparaat-beheerder is toegewezen om een apparaat te beheren. 

## <a name="manage-the-global-administrators-role"></a>De rol globale beheerder beheren

Zie voor het weer geven en bijwerken van het lidmaatschap van de rol globale beheerder:

- [Alle leden van een beheerdersrol weer geven in Azure Active Directory](../roles/manage-roles-portal.md)
- [Beheerdersrollen toewijzen aan gebruikers in Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>De rol van Apparaatbeheer beheren 

In de Azure Portal, kunt u de rol van Apparaatbeheer op de pagina **apparaten** beheren. De pagina **apparaten** openen:

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com) als globale beheerder.
1. Zoek en selecteer de optie *Azure Active Directory*.
1. Klik in de sectie **beheren** op **apparaten**.
1. Klik op de pagina **apparaten** op **Apparaatinstellingen**.

Als u de rol van Apparaatbeheer wilt wijzigen, configureert u **aanvullende lokale beheerders op aan Azure AD gekoppelde apparaten**.  

![Aanvullende lokale beheerders](./media/assign-local-admin/10.png)

>[!NOTE]
> Voor deze optie is een Azure AD Premium-Tenant vereist. 

Apparaat beheerders worden toegewezen aan alle aan Azure AD gekoppelde apparaten. U kunt geen apparaat beheerders bereiken aan een specifieke set apparaten. Het bijwerken van de rol van de beheerderrol heeft geen directe gevolgen voor de betrokken gebruikers. Op apparaten waarop al een gebruiker is aangemeld, vindt de uitbrei ding van bevoegdheden plaats wanneer *beide* onderstaande acties optreden:

- Er zijn vier uur door gegeven voor Azure AD om een nieuw primair vernieuwings token met de juiste bevoegdheden uit te geven. 
- Gebruiker meldt zich af en meldt zich weer aan, niet vergren delen/ontgrendelen, om het profiel te vernieuwen.

>[!NOTE]
> De bovenstaande acties zijn niet van toepassing op gebruikers die eerder niet zijn aangemeld bij het betreffende apparaat. In dit geval worden de beheerders bevoegdheden onmiddellijk toegepast na de eerste aanmelding bij het apparaat. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Beheerders bevoegdheden beheren met Azure AD-groepen (preview-versie)

>[!NOTE]
> Deze functie is momenteel beschikbaar als preview-product.


Vanaf de update voor Windows 10 2004 kunt u Azure AD-groepen gebruiken voor het beheren van Administrator bevoegdheden op apparaten die zijn toegevoegd aan Azure AD met het MDM-beleid voor [beperkte groepen](/windows/client-management/mdm/policy-csp-restrictedgroups) . Met dit beleid kunt u afzonderlijke gebruikers of Azure AD-groepen toewijzen aan de lokale groep Administrators op een toegevoegd Azure AD-apparaat, zodat u de granulariteit kunt configureren om afzonderlijke beheerders voor verschillende groepen apparaten in te stellen. 

>[!NOTE]
> Het starten van de update voor Windows 10 20H2 wordt aangeraden om het beleid voor [lokale gebruikers en groepen](/windows/client-management/mdm/policy-csp-localusersandgroups) te gebruiken in plaats van het beleid voor beperkte groepen


Er is momenteel geen gebruikers interface in intune voor het beheren van dit beleid en ze moeten worden geconfigureerd met behulp van [aangepaste oma-URI-instellingen](/mem/intune/configuration/custom-settings-windows-10). Enkele overwegingen voor het gebruik van een van deze beleids regels: 

- Voor het toevoegen van Azure AD-groepen via het beleid is de SID van de groep vereist die kan worden verkregen door de [Microsoft Graph-API voor groepen](/graph/api/resources/group?view=graph-rest-beta)uit te voeren. De SID wordt gedefinieerd door de eigenschap `securityIdentifier` in de API-reactie.
- Wanneer het beleid voor beperkte groepen wordt afgedwongen, wordt het huidige lid van de groep die zich niet in de lijst met leden bevindt, verwijderd. Als u dit beleid afdwingt met nieuwe leden of groepen, worden de bestaande beheerders verwijderd, namelijk de gebruiker die lid is van het apparaat, de rol van Apparaatbeheer en de rol van globale beheerder van het apparaat. Als u wilt voor komen dat bestaande leden worden verwijderd, moet u deze configureren als onderdeel van de lijst met leden in het beleid voor beperkte groepen. Deze beperking is gericht als u het beleid lokale gebruikers en groepen gebruikt waarmee incrementele updates voor groepslid maatschappen worden toegestaan
- Beheerders bevoegdheden die beide beleids regels gebruiken, worden alleen geëvalueerd voor de volgende bekende groepen op een Windows 10-apparaat-Administrators, gebruikers, gasten, hoofd gebruikers, Extern bureaublad gebruikers en gebruikers van extern beheer. 
- Het beheren van lokale beheerders met Azure AD-groepen is niet van toepassing op hybride Azure AD-gekoppelde of Azure AD-geregistreerde apparaten.
- Hoewel het beleid voor beperkte groepen bestond vóór Windows 10 2004-Update, heeft het geen ondersteuning voor Azure AD-groepen als leden van de lokale groep Administrators van een apparaat. 

## <a name="manage-regular-users"></a>Reguliere gebruikers beheren

Standaard voegt Azure AD de gebruiker die de Azure AD-koppeling uitvoert, toe aan de groep Administrators op het apparaat. Als u wilt voor komen dat gewone gebruikers lokale beheerders worden, hebt u de volgende opties:

- [Windows auto pilot](/windows/deployment/windows-autopilot/windows-10-autopilot) : Windows auto pilot biedt u de mogelijkheid om te voor komen dat een primaire gebruiker die de koppeling uitvoert, een lokale beheerder wordt. U kunt dit doen door [een auto pilot-profiel te maken](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Bulk inschrijving](/intune/windows-bulk-enroll) : een Azure AD-deelname die wordt uitgevoerd in de context van een bulk registratie, gebeurt in de context van een automatisch gemaakte gebruiker. Gebruikers die zich aanmelden nadat een apparaat is toegevoegd, worden niet toegevoegd aan de groep Administrators.   

## <a name="manually-elevate-a-user-on-a-device"></a>Een gebruiker op een apparaat hand matig verhogen 

Naast het gebruik van het Azure AD-lidmaatschaps proces, kunt u een gewone gebruiker ook hand matig een lokale beheerder worden voor een specifiek apparaat. Voor deze stap moet u al lid zijn van de lokale groep Administrators. 

Vanaf de **Windows 10 1709** -release kunt u deze taak uitvoeren vanuit **instellingen-> accounts-> andere gebruikers**. Selecteer **een werk-of school gebruiker toevoegen**, voer de UPN van de gebruiker onder **gebruikers account** in en selecteer *beheerder* onder **account type**  
 
Daarnaast kunt u ook gebruikers toevoegen met behulp van de opdracht prompt:

- Als uw Tenant gebruikers zijn gesynchroniseerd vanuit een on-premises Active Directory, gebruikt u `net localgroup administrators /add "Contoso\username"` .
- Als uw Tenant gebruikers zijn gemaakt in azure AD, gebruikt u `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Overwegingen 

U kunt geen groepen toewijzen aan de rol apparaat-beheerder, alleen afzonderlijke gebruikers zijn toegestaan.

Apparaat beheerders worden toegewezen aan alle aan Azure AD gekoppelde apparaten. Ze kunnen niet worden toegewezen aan een specifieke set apparaten.

Wanneer u gebruikers van de rol apparaat-beheerder verwijdert, hebben ze nog steeds de lokale beheerders bevoegdheden op een apparaat zolang ze hiervoor zijn aangemeld. De bevoegdheid wordt ingetrokken tijdens de volgende aanmelding wanneer een nieuw primair vernieuwings token is uitgegeven. Deze intrekking, vergelijkbaar met de uitbrei ding van bevoegdheden, kan tot 4 uur duren.

## <a name="next-steps"></a>Volgende stappen

- Zie [Apparaten beheren met behulp van de Azure-portal](device-management-azure-portal.md) voor een overzicht van hoe u apparaten in de Azure-portal kunt beheren.
- Zie [Azure Active Directory op apparaten gebaseerde beleids regels voor voorwaardelijke toegang configureren](../conditional-access/require-managed-devices.md)voor meer informatie over voorwaardelijke toegang op basis van apparaten.
