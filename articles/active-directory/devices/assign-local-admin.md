---
title: Lokale beheerders beheren op apparaten die zijn verbonden met Azure AD
description: Meer informatie over het toewijzen van Azure-rollen aan de lokale beheerdersgroep van een Windows-apparaat.
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
ms.openlocfilehash: 56e0f92593d185890e34a1a5120093d68cf45484
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388412"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>De lokale beheerdersgroep beheren op apparaten die zijn verbonden met Azure AD

Als u een Windows-apparaat wilt beheren, moet u lid zijn van de lokale beheerdersgroep. Als onderdeel van het Azure Active Directory (Azure AD) join werkt Azure AD het lidmaatschap van deze groep op een apparaat bij. U kunt de lidmaatschapsupdate aanpassen om te voldoen aan uw zakelijke vereisten. Een lidmaatschapsupdate is bijvoorbeeld handig als u uw helpdeskmedewerkers in staat wilt stellen taken uit te voeren waarvoor beheerdersrechten op een apparaat zijn vereist.

In dit artikel wordt uitgelegd hoe het bijwerken van het lidmaatschap van lokale beheerders werkt en hoe u deze kunt aanpassen tijdens een Azure AD Join. De inhoud van dit artikel is niet van toepassing op apparaten die zijn samengevoegd met **hybride Azure AD.**

## <a name="how-it-works"></a>Uitleg

Wanneer u een Windows-apparaat verbindt met Azure AD met behulp van een Azure AD-join, voegt Azure AD de volgende beveiligingsprincipads toe aan de lokale beheerdersgroep op het apparaat:

- De globale beheerdersrol van Azure AD
- De rol van Azure AD-apparaatbeheerder 
- De gebruiker die de Azure AD-join uitvoeren   

Door Azure AD-rollen toe te voegen aan de lokale beheerdersgroep, kunt u de gebruikers bijwerken die een apparaat op elk gewenst moment in Azure AD kunnen beheren zonder iets op het apparaat te wijzigen. Azure AD voegt ook de Azure AD-apparaatbeheerderrol toe aan de lokale beheerdersgroep ter ondersteuning van het principe van de minste bevoegdheden (PoLP). Naast de globale beheerders kunt u gebruikers aan  wie alleen de rol apparaatbeheerder is toegewezen, inschakelen om een apparaat te beheren. 

## <a name="manage-the-global-administrators-role"></a>De rol van globale beheerder beheren

Als u het lidmaatschap van de globale beheerdersrol wilt weergeven en bijwerken, gaat u naar:

- [Alles weergeven van een beheerdersrol in Azure Active Directory](../roles/manage-roles-portal.md)
- [Beheerdersrollen toewijzen aan gebruikers in Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>De rol apparaatbeheerder beheren 

In de Azure Portal kunt u de rol apparaatbeheerder beheren op de **pagina** Apparaten. De pagina **Apparaten** openen:

1. Meld u als globale [Azure Portal](https://portal.azure.com) aan bij uw bedrijf.
1. Zoek en selecteer de optie *Azure Active Directory*.
1. Klik in **de sectie** Beheren op **Apparaten.**
1. Klik op **de pagina** Apparaten op **Apparaatinstellingen.**

Als u de rol van apparaatbeheerder wilt wijzigen, **configureert u Aanvullende lokale beheerders op apparaten die zijn samengevoegd met Azure AD.**  

![Aanvullende lokale beheerders](./media/assign-local-admin/10.png)

>[!NOTE]
> Voor deze optie is een Azure AD Premium tenant vereist. 

Apparaatbeheerders worden toegewezen aan alle apparaten die zijn samengevoegd met Azure AD. U kunt apparaatbeheerders niet instellen op een specifieke set apparaten. Het bijwerken van de rol apparaatbeheerder heeft niet noodzakelijkerwijs een onmiddellijke invloed op de betrokken gebruikers. Op apparaten waarbij een gebruiker al is aangemeld, vindt de verhoging van bevoegdheden plaats wanneer *beide* onderstaande acties plaatsvinden:

- Er zijn maximaal vier uur verstreken voor Azure AD om een nieuw primair vernieuwingstoken met de juiste bevoegdheden uit te geven. 
- De gebruiker meldt zich af en weer aan, niet vergrendelen/ontgrendelen, om het profiel te vernieuwen.

>[!NOTE]
> De bovenstaande acties zijn niet van toepassing op gebruikers die zich niet eerder bij het relevante apparaat hebben aangemeld. In dit geval worden de beheerdersbevoegdheden direct na de eerste aanmelding op het apparaat toegepast. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Beheerdersbevoegdheden beheren met Behulp van Azure AD-groepen (preview)

Vanaf Windows 10 versie 2004 kunt u Azure AD-groepen gebruiken om beheerdersbevoegdheden [](/windows/client-management/mdm/policy-csp-restrictedgroups) te beheren op azure AD-apparaten met het MDM-beleid Beperkte groepen. Met dit beleid kunt u afzonderlijke gebruikers of Azure AD-groepen toewijzen aan de lokale beheerdersgroep op een apparaat dat is samengevoegd met Azure AD, zodat u de granulariteit hebt om afzonderlijke beheerders voor verschillende groepen apparaten te configureren. 

>[!NOTE]
> Vanaf Windows 10 update van 20h2 wordt [](/windows/client-management/mdm/policy-csp-localusersandgroups) u aangeraden het beleid Lokale gebruikers en groepen te gebruiken in plaats van het beleid Voor beperkte groepen


Er is momenteel geen gebruikersinterface in Intune om dit beleid te beheren en ze moeten worden geconfigureerd met aangepaste [OMA-URI-instellingen.](/mem/intune/configuration/custom-settings-windows-10) Enkele overwegingen voor het gebruik van een van deze beleidsregels: 

- Voor het toevoegen van Azure AD-groepen via het beleid is de SID van de groep vereist die kan worden verkregen door de API voor [groepen Microsoft Graph uit te voeren.](/graph/api/resources/group?view=graph-rest-beta) De SID wordt gedefinieerd door de eigenschap `securityIdentifier` in het API-antwoord.
- Wanneer het beleid voor beperkte groepen wordt afgedwongen, wordt elk huidig lid van de groep verwijderd dat niet op de lijst Leden staat. Als u dit beleid afdwingt met nieuwe leden of groepen, worden de bestaande beheerders, namelijk de gebruiker die lid is van het apparaat, de rol Apparaatbeheerder en Globale beheerder verwijderd van het apparaat. Als u wilt voorkomen dat bestaande leden worden verwijderd, moet u deze configureren als onderdeel van de lijst Leden in het beleid Voor beperkte groepen. Deze beperking wordt verholpen als u het beleid Lokale gebruikers en groepen gebruikt dat incrementele updates van groepslidmaatschap toestaat
- Beheerdersbevoegdheden die beide beleidsregels gebruiken, worden alleen geëvalueerd voor de volgende bekende groepen op een Windows 10-apparaat: beheerders, gebruikers, gasten, energiegebruikers, Extern bureaublad gebruikers en gebruikers van extern beheer. 
- Het beheren van lokale beheerders met behulp van Azure AD-groepen is niet van toepassing op hybride Azure AD-apparaten of apparaten die zijn geregistreerd bij Azure AD.
- Hoewel het beleid voor beperkte groepen bestond vóór Windows 10 versie 2004, ondersteunde het geen Azure AD-groepen als leden van de lokale beheerdersgroep van een apparaat. 

## <a name="manage-regular-users"></a>Normale gebruikers beheren

Standaard voegt Azure AD de gebruiker die de Azure AD-join uitvoeren toe aan de beheerdersgroep op het apparaat. Als u wilt voorkomen dat gewone gebruikers lokale beheerders worden, hebt u de volgende opties:

- [Windows Autopilot:](/windows/deployment/windows-autopilot/windows-10-autopilot) Windows Autopilot biedt u een optie om te voorkomen dat primaire gebruikers die de join uitvoeren een lokale beheerder worden. U kunt dit doen door een [Autopilot-profiel te maken.](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)
- [Bulkinschrijving:](/intune/windows-bulk-enroll) een Azure AD-join die wordt uitgevoerd in de context van een bulkinschrijving vindt plaats in de context van een automatisch gemaakte gebruiker. Gebruikers die zich aanmelden nadat een apparaat is toegevoegd, worden niet toegevoegd aan de beheerdersgroep.   

## <a name="manually-elevate-a-user-on-a-device"></a>Een gebruiker handmatig verhogen op een apparaat 

Naast het gebruik van het Azure AD-joinproces kunt u een gewone gebruiker ook handmatig verhogen om een lokale beheerder te worden op één specifiek apparaat. Voor deze stap moet u al lid zijn van de lokale beheerdersgroep. 

Vanaf versie **Windows 10 versie 1709** kunt u deze taak uitvoeren via Instellingen **-> Accounts -> Andere gebruikers.** Selecteer **Een werk- of schoolgebruiker toevoegen,** voer de UPN van de gebruiker in onder **Gebruikersaccount** en selecteer *Beheerder* onder **Accounttype**  
 
Daarnaast kunt u gebruikers toevoegen met behulp van de opdrachtprompt:

- Als uw tenantgebruikers worden gesynchroniseerd vanuit on-premises Active Directory, gebruikt u `net localgroup administrators /add "Contoso\username"` .
- Als uw tenantgebruikers zijn gemaakt in Azure AD, gebruikt u `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Overwegingen 

U kunt geen groepen toewijzen aan de rol apparaatbeheerder. Alleen individuele gebruikers zijn toegestaan.

Apparaatbeheerders worden toegewezen aan alle apparaten die zijn samengevoegd met Azure AD. Ze kunnen niet worden beperkt tot een specifieke set apparaten.

Wanneer u gebruikers uit de rol apparaatbeheerder verwijdert, hebben ze nog steeds de lokale beheerdersrechten op een apparaat zolang ze zijn aangemeld bij het apparaat. De bevoegdheid wordt ingetrokken tijdens de volgende aanmelding wanneer een nieuw primair vernieuwings token wordt uitgegeven. Deze intrekking, vergelijkbaar met de verhoging van bevoegdheden, kan tot vier uur duren.

## <a name="next-steps"></a>Volgende stappen

- Zie [Apparaten beheren met behulp van de Azure-portal](device-management-azure-portal.md) voor een overzicht van hoe u apparaten in de Azure-portal kunt beheren.
- Zie Beleid voor voorwaardelijke toegang op basis van apparaten configureren Azure Active Directory apparaat voor meer informatie over voorwaardelijke toegang [op basis van apparaten.](../conditional-access/require-managed-devices.md)
