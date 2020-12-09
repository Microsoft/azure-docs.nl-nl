---
title: Problemen met rollen oplossen die zijn toegewezen aan Cloud groep Veelgestelde vragen-Azure Active Directory | Microsoft Docs
description: Meer informatie over veelgestelde vragen en tips voor probleem oplossing voor het toewijzen van rollen aan groepen in Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8735a0d34b9fcf5b86b6592980ffc5c7c3e3073c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861932"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>Problemen met rollen die zijn toegewezen aan cloudgroepen oplossen

Hier volgen enkele veelgestelde vragen en tips voor probleem oplossing voor het toewijzen van rollen aan groepen in Azure Active Directory (Azure AD).

**V:** Ik ben groeps beheerder, maar ik kan de **Azure AD-rollen niet zien aan de groep** switch.

**A:** Alleen bevoegde beheerdersrol of globale beheerders kunnen een groep maken die in aanmerking komt voor roltoewijzing. Dit besturings element is alleen zichtbaar voor gebruikers in deze rollen.

**V:** Wie kan het lidmaatschap wijzigen van groepen die zijn toegewezen aan Azure AD-rollen?

**A:** Standaard beheert alleen de beheerder van de bevoegde rol en de globale beheerder het lidmaatschap van een door een functie toewijs bare groep, maar u kunt het beheer van aan rollen toewijs bare groepen delegeren door groeps eigenaren toe te voegen.

**V**: Ik ben een helpdesk beheerder in mijn organisatie, maar ik kan het wacht woord van een gebruiker die een directory lezer is, niet bijwerken. Waarom gebeurt dit?

**A**: de gebruiker heeft mogelijk een gemapeerde Directory lezer met een door een functie toewijs bare groep. Alle leden en eigen aren van een door een functie toewijs bare groep zijn beveiligd. Alleen gebruikers met de rol privileged Authentication Administrator of globale beheerder kunnen referenties voor een beveiligde gebruiker opnieuw instellen.

**V:** Ik kan het wacht woord van een gebruiker niet bijwerken. Er is geen rol met een hoger privilege toegewezen aan de gebruiker. Waarom gebeurt dit?

**A:** De gebruiker kan een eigenaar zijn van een door een functie toewijs bare groep. Wij beveiligen eigen aren van door rollen toewijs bare groepen om misbruik van bevoegdheden te voor komen. Een voor beeld hiervan kan zijn als er een groep Contoso_Security_Admins is toegewezen aan de rol beveiligings beheerder, waarbij Bob de groeps eigenaar is en ANNEER is wachtwoord beheerder in de organisatie. Als deze beveiliging niet aanwezig is, kan Anja de referenties van Dirk opnieuw instellen en zijn identiteit overnemen. Daarna kan Anne zichzelf of iedereen toevoegen aan de groep Contoso_Security_Admins groep om een beveiligings beheerder in de organisatie te worden. Als u wilt weten of een gebruiker een groeps eigenaar is, haalt u de lijst met objecten in eigendom van die gebruiker op en controleert u of voor een van de groepen isAssignableToRole is ingesteld op True. Zo ja, dan is die gebruiker beveiligd en is het gedrag inherent aan het ontwerp. Raadpleeg deze documentes voor het ophalen van objecten in eigendom:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject)  
- [OwnedObjects weer geven](/graph/api/user-list-ownedobjects?tabs=http)

**V:** Kan ik een toegangs beoordeling maken voor groepen die kunnen worden toegewezen aan Azure AD-rollen (met name groepen waarvoor de eigenschap isAssignableToRole is ingesteld op waar)?  

**A:** Ja, dat kunt u. Als u de nieuwste versie van de toegangs beoordeling hebt, worden uw revisoren standaard naar mijn toegang geleid en kunnen alleen globale beheerders toegangs beoordelingen maken voor door rollen toewijs bare groepen. Als u echter een oudere versie van de toegangs beoordeling hebt, worden uw revisoren standaard naar het toegangs paneel geleid en kunnen globale beheerders en gebruikers beheerders toegangs beoordelingen maken voor door rollen toewijs bare groepen. De nieuwe ervaring wordt op 28 juli 2020 door gegeven aan alle klanten, maar als u eerder een upgrade wilt uitvoeren, moet u een aanvraag indienen voor [Azure AD-toegangs beoordelingen-bijgewerkte revisor ervaring in mijn Access-aanmelding](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

**V:** Kan ik een toegangs pakket maken en groepen plaatsen die aan Azure AD-rollen kunnen worden toegewezen?

**A:** Ja, dat kunt u. Globale beheerder en gebruikers beheerder hebben de kracht om een groep in een toegangs pakket te plaatsen. Er zijn geen wijzigingen voor de globale beheerder, maar er is een kleine wijziging in de machtigingen voor de rol van gebruikers beheerder. Als u een door een functie toewijs bare groep wilt opnemen in een toegangs pakket, moet u een gebruikers beheerder zijn en ook eigenaar zijn van de groep die kan worden toegewezen. Hier is de volledige tabel waarin wordt weer gegeven wie een toegangs pakket kan maken in Enter prise License Management:

Azure AD-adreslijst functie | Rol van rechten beheer | Kan beveiligings groep toevoegen\* | Kan Microsoft 365 groep toevoegen\* | Kan app toevoegen | Kan de share point online-site toevoegen
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Globale beheerder | n.v.t. | ✔️ | ✔️ | ✔️  | ✔️
Gebruikersbeheerder  | n.v.t.  | ✔️  | ✔️  | ✔️
Intune-beheerder | Catalogus eigenaar | ✔️  | ✔️  | &nbsp;  | &nbsp;
Exchange-beheerder  | Catalogus eigenaar  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Teams service-beheerder | Catalogus eigenaar  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
SharePoint-beheerder | Catalogus eigenaar | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Toepassings beheerder | Catalogus eigenaar  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Beheerder van de Cloud toepassing | Catalogus eigenaar  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Gebruiker | Catalogus eigenaar | Alleen als de groeps eigenaar | Alleen als de groeps eigenaar | Alleen als de eigenaar van de app  | &nbsp;

\*Groep is geen rol die kan worden toegewezen. dat wil zeggen, isAssignableToRole = false. Als een groep rol kan worden toegewezen, moet de persoon die het toegangs pakket maakt ook eigenaar zijn van de groep die kan worden toegewezen.

**V:** Ik kan de optie toewijzing verwijderen niet vinden in toegewezen rollen. Roltoewijzing Hoe kan ik verwijderen aan een gebruiker?

**A:** Dit antwoord is alleen van toepassing op Azure AD Premium P1-organisaties.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en open **Azure Active Directory**.
1. Selecteer gebruikers en open een gebruikers profiel.
1. Selecteer **toegewezen rollen**.
1. Selecteer het tandwiel pictogram. Er wordt een deel venster geopend waarin u deze informatie kunt opgeven. Er is een knop verwijderen naast directe toewijzingen. Als u een indirecte roltoewijzing wilt verwijderen, verwijdert u de gebruiker uit de groep waaraan de rol is toegewezen.

**V:** Hoe kan ik alle groepen weer geven die door een rol kunnen worden toegewezen?

**A:** Volg deze stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en open **Azure Active Directory**.
1. Selecteer **groepen**  >  **alle groepen**.
1. Selecteer **filters toevoegen**.
1. Filteren op **toegewezen rol**.

**V:** Hoe kan ik weet welke rol direct en indirect aan een principal worden toegewezen?

**A:** Volg deze stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en open **Azure Active Directory**.
1. Selecteer gebruikers en open een gebruikers profiel.
1. Selecteer **toegewezen rollen** en klik vervolgens op:

    - In Azure AD Premium P1-gelicentieerde organisaties: Selecteer het tandwiel pictogram. Er wordt een deel venster geopend waarin u deze informatie kunt opgeven.
    - In Azure AD Premium P2-licenties voor licentie verlening: u vindt directe en overgenomen licentie gegevens in de kolom **lidmaatschap** .

**V:** Waarom wordt het maken van een nieuwe Cloud groep afgedwongen voor toewijzing aan rol?  

**A:** Als u een bestaande groep toewijst aan een rol, kan de bestaande groeps eigenaar andere leden toevoegen aan deze groep zonder de nieuwe leden te realiseren dat ze de rol hebben. Omdat groepen die aan rollen kunnen worden toegewezen krachtig zijn, worden er veel beperkingen gesteld om ze te beveiligen. U wilt geen wijzigingen in de groep die worden verrassende aan degene die de groep beheert.

## <a name="next-steps"></a>Volgende stappen

- [Cloudgroepen gebruiken om roltoewijzingen te beheren](groups-concept.md)
- [Een roltoewijzbare groep maken](groups-create-eligible.md)
