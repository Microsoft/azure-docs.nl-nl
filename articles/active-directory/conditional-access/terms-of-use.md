---
title: Gebruiksvoorwaarden-Azure Active Directory | Microsoft Docs
description: Ga aan de slag met Azure Active Directory gebruiks voorwaarden om informatie aan werk nemers of gasten te presen teren voordat ze toegang krijgen.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 12/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1829c69510568b0f9a8cec7fb7d2d57be8515d3
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509985"
---
# <a name="azure-active-directory-terms-of-use"></a>Gebruiks voorwaarden van Azure Active Directory

Azure AD-gebruiks voorwaarden bieden een eenvoudige methode die organisaties kunnen gebruiken om informatie te presen teren aan eind gebruikers. Deze presentatie zorgt ervoor dat gebruikers relevante disclaimers voor juridische vereisten of nalevingsvereisten te zien krijgen. In dit artikel wordt beschreven hoe u aan de slag gaat met gebruiks voorwaarden.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Overzicht Video's

De volgende video bevat een kort overzicht van gebruiks voorwaarden.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Zie voor meer Video's:
- [Gebruiks voorwaarden in Azure Active Directory implementeren](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Gebruiks voorwaarden in Azure Active Directory samen vouwen](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Wat kan ik doen met gebruiks voorwaarden?

Azure AD-gebruiks voorwaarden hebben de volgende mogelijkheden:

- Vereisen dat werk nemers of gasten uw gebruiks voorwaarden accepteren voordat ze toegang krijgen.
- Vereisen dat werk nemers of gasten uw gebruiks voorwaarden op elk apparaat accepteren voordat ze toegang krijgen.
- Vereisen dat werk nemers of gasten uw gebruiks voorwaarden in een terugkerend schema accepteren.
- Vereisen dat werk nemers of gasten uw gebruiks voorwaarden accepteren voordat ze beveiligings gegevens registreren in azure AD Multi-Factor Authentication (MFA).
- Vereisen dat werk nemers uw gebruiks voorwaarden accepteren voordat ze beveiligings gegevens registreren in azure AD selfservice voor wachtwoord herstel (SSPR).
- Algemene Gebruiks voorwaarden voor alle gebruikers in uw organisatie.
- Specifieke gebruiks voorwaarden presen teren op basis van de kenmerken van een gebruiker (bijvoorbeeld artsen versus verpleegkundigen of binnenlandse werknemers versus werknemers in het buitenland) met behulp van [dynamische groepen](../enterprise-users/groups-dynamic-membership.md).
- Specifieke gebruiks voorwaarden presen teren bij het openen van toepassingen met hoge bedrijfs impact, zoals Sales Force.
- Gebruiks voorwaarden in verschillende talen.
- Een lijst die uw gebruiks voorwaarden al dan niet heeft geaccepteerd.
- Hulp bij het beantwoorden van privacy-regelgeving.
- Een logboek met gebruiks voorwaarden voor naleving en controle weer geven.
- Gebruiks voorwaarden maken en beheren met behulp van [Microsoft Graph api's](/graph/api/resources/agreement?view=graph-rest-beta) (momenteel als preview-versie).

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-gebruiks voorwaarden wilt gebruiken en configureren, hebt u het volgende nodig:

- Abonnement op Azure AD Premium P1, P2, EMS E3 of EMS E5.
   - Als u niet over een van deze abonnementen beschikt, kunt u [Azure AD Premium downloaden](../fundamentals/active-directory-get-started-premium.md) of [Azure AD Premium uitproberen](https://azure.microsoft.com/trial/get-started-active-directory/).
- Een van de volgende beheerdersaccounts voor de directory die u wilt configureren:
   - Hoofdbeheerder
   - Beveiligingsbeheer
   - Beheerder van voorwaardelijke toegang

## <a name="terms-of-use-document"></a>Document met gebruiksrechtovereenkomst

Voor de gebruiks voorwaarden van Azure AD wordt de PDF-indeling gebruikt om inhoud weer te geven. Het PDF-bestand kan van alles bevatten, zoals bestaande contracten, zodat u tijdens het aanmelden van gebruikers eindgebruikersovereenkomsten kunt verzamelen. Ter ondersteuning van gebruikers op mobiele apparaten is de aanbevolen teken grootte in de PDF 24 punten.

## <a name="add-terms-of-use"></a>Gebruiks voorwaarden toevoegen

Wanneer u het document voor de gebruiks voorwaarden hebt voltooid, gebruikt u de volgende procedure om het te voegen.

1. Meld u aan bij Azure als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).

   ![Voorwaardelijke toegang-Blade Gebruiksvoorwaarden](./media/terms-of-use/tou-blade.png)

1. Klik op **Nieuwe voorwaarden**.

   ![Het deel venster nieuwe gebruiks voorwaarden om uw gebruiksrecht overeenkomst op te geven](./media/terms-of-use/new-tou.png)

1. Voer in het vak **naam** een naam in voor de gebruiks voorwaarden die worden gebruikt in de Azure Portal.
1. Voer in het vak **weergave naam** een titel in die gebruikers zien wanneer ze zich aanmelden.
1. Voor **Gebruiksvoorwaarden document** bladert u naar uw voltooide voor waarden van PDF en selecteert u deze.
1. Selecteer de taal voor uw gebruiks voorwaarden document. Via de taaloptie kunt u meerdere gebruiksvoorwaarden uploaden, elk met een andere taal. Welke versie van de gebruiksvoorwaarden aan een eindgebruiker wordt weergegeven, is gebaseerd op de browservoorkeuren.
1. Als u wilt dat eind gebruikers de gebruiks voorwaarden kunnen bekijken voordat ze ze kunnen accepteren, stelt u **gebruikers verplichten om de gebruiks voorwaarden uit te breiden** naar **aan.**
1. Als eind gebruikers de gebruiks voorwaarden moeten accepteren op elk apparaat waartoe ze toegang hebben, stelt u **gebruikers verplicht om op elk apparaat** in te stemmen **.** Gebruikers kunnen verplicht zijn om extra toepassingen te installeren als deze optie is ingeschakeld. Zie [gebruiks voorwaarden per apparaat](#per-device-terms-of-use)voor meer informatie.
1. Als u de voor waarden voor het gebruik van de gebruiksrecht overeenkomst volgens een planning wilt laten verlopen, stelt u **verlopende berichten** **in op aan.** Als deze functie is ingesteld op on, worden er twee aanvullende schema-instellingen weer gegeven.

   ![Instellingen voor verlopen verloopt om de begin datum, frequentie en duur in te stellen](./media/terms-of-use/expire-consents.png)

1. Gebruik de instellingen **verloopt op basis van begin datum** en **frequentie** om de planning op te geven voor de gebruiks voorwaarden. In de volgende tabel ziet u het resultaat voor enkele voor beelden van instellingen:

   | Verloopt vanaf | Frequentie | Resultaat |
   | --- | --- | --- |
   | De datum van vandaag  | Maandelijks | Vanaf vandaag moeten gebruikers de gebruiks voorwaarden accepteren en vervolgens elke maand opnieuw accepteren. |
   | Datum in de toekomst  | Maandelijks | Gebruikers moeten de gebruiks voorwaarden accepteren om vandaag nog te beginnen. Wanneer de datum in de toekomst plaatsvindt, verloopt de toestemmingen en worden de gebruikers elke maand opnieuw geaccepteerd.  |

   Als u bijvoorbeeld de verlopende begin datum instelt op **1 januari** en frequentie tot **maandelijks**, is het mogelijk dat er verloop voor twee gebruikers kan optreden:

   | Gebruiker | Eerste acceptatie datum | Datum eerste verloop | Datum van de tweede verloopt | Datum van derde verloop |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 1 februari | 1 maart | Apr 1 |
   | Bob | 15 jan | 1 februari | 1 maart | Apr 1 |

1. Gebruik de instelling **duur vóór opnieuw accepteren (dagen)** om het aantal dagen op te geven waarna de gebruiker de gebruiks voorwaarden opnieuw moet accepteren. Hiermee kunnen gebruikers hun eigen schema volgen. Als u bijvoorbeeld de duur instelt op **30** dagen, ziet u hier hoe verloop tijd voor twee gebruikers kan optreden:

   | Gebruiker | Eerste acceptatie datum | Datum eerste verloop | Datum van de tweede verloopt | Datum van derde verloop |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 31 januari | 2 maart | Apr 1 |
   | Bob | 15 jan | 14 februari | 16 mrt | Apr 15 |

   Het is mogelijk om de instellingen voor **verlopende** en duur te gebruiken **voordat de heracceptatie (dagen)** wordt ingesteld op samen, maar normaal gesp roken gebruikt u een van beide.

1. Gebruik onder **voorwaardelijke toegang** de lijst **afdwingen met beleids sjablonen voor voorwaardelijke toegang** om de sjabloon te selecteren voor het afdwingen van de gebruiks voorwaarden.

   ![De vervolg keuzelijst voorwaardelijke toegang om een beleids sjabloon te selecteren](./media/terms-of-use/conditional-access-templates.png)

   | Template | Beschrijving |
   | --- | --- |
   | **Toegang tot Cloud-apps voor alle gasten** | Er wordt een beleid voor voorwaardelijke toegang gemaakt voor alle gasten en alle Cloud-apps. Dit beleid is van invloed op de Azure Portal. Nadat deze is gemaakt, moet u zich mogelijk afmelden en aanmelden. |
   | **Toegang tot Cloud-apps voor alle gebruikers** | Er wordt een beleid voor voorwaardelijke toegang gemaakt voor alle gebruikers en alle Cloud-apps. Dit beleid is van invloed op de Azure Portal. Zodra deze is gemaakt, moet u zich afmelden en aanmelden. |
   | **Aangepast beleid** | Selecteer de gebruikers, groepen en apps waarop deze gebruiks voorwaarden worden toegepast. |
   | **Beleid voor voorwaardelijke toegang later maken** | Deze gebruiks voorwaarden worden weer gegeven in de lijst granting Control bij het maken van beleid voor voorwaardelijke toegang. |

   >[!IMPORTANT]
   >Besturings elementen voor beleid voor voorwaardelijke toegang (met inbegrip van gebruiks voorwaarden) bieden geen ondersteuning voor het afdwingen van service accounts. U wordt aangeraden alle service accounts uit te sluiten van het beleid voor voorwaardelijke toegang.

    Met het aangepaste beleid voor voorwaardelijke toegang worden nauw keurige gebruiks voorwaarden ingeschakeld, tot een bepaalde Cloud toepassing of groep gebruikers. Voor meer informatie raadpleegt [u Quick Start: vereisen dat de gebruiks voorwaarden worden geaccepteerd voor toegang tot Cloud-apps](require-tou.md).

1. Klik op **Create**.

   Als u een aangepaste sjabloon voor voorwaardelijke toegang hebt geselecteerd, wordt er een nieuw scherm weer gegeven waarin u het aangepaste beleid voor voorwaardelijke toegang kunt maken.

   ![Het deel venster nieuwe voorwaardelijke toegang als u de sjabloon voor het aangepaste beleid voor voorwaardelijke toegang hebt gekozen](./media/terms-of-use/custom-policy.png)

   U ziet nu de nieuwe gebruiks voorwaarden.

   ![Nieuwe gebruiks voorwaarden die worden vermeld op de Blade voor de voor waarden van het gebruik](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Rapport met geaccepteerde en geweigerde rapporten weer geven

In de Gebruiksrechtovereenkomst-blade ziet u de aantallen gebruikers die al dan niet hebben geaccepteerd. Deze aantallen en personen die zijn geaccepteerd/geweigerd, worden opgeslagen voor de gebruiks duur van de gebruiks voorwaarden.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).

   ![Gebruiksvoorwaarden Blade met vermelding van het aantal gebruikers weer geven dat is geaccepteerd en geweigerd](./media/terms-of-use/view-tou.png)

1. Voor gebruiks voorwaarden klikt u op de nummers onder **geaccepteerd** of **geweigerd** om de huidige status voor gebruikers weer te geven.

   ![Gebruiksvoorwaarden deel venster met toestemmingen worden de gebruikers weer gegeven die zijn geaccepteerd](./media/terms-of-use/accepted-tou.png)

1. Als u de geschiedenis voor een afzonderlijke gebruiker wilt weer geven, klikt u op het weglatings teken (**...**) en bekijkt u de **geschiedenis**.

   ![Context menu Geschiedenis voor een gebruiker weer geven](./media/terms-of-use/view-history-menu.png)

   In het deel venster geschiedenis weer geven ziet u een geschiedenis van alle geaccepteerde, geweigerde en verlopen.

   ![Deel venster geschiedenis weer geven bevat de geschiedenis accepteren, afwijzen en verlopen voor een gebruiker](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Audit logboeken van Azure AD weer geven

Als u aanvullende activiteiten wilt weer geven, bevatten Azure AD-gebruiks voorwaarden audit Logboeken. Elke toestemming van de gebruiker activeert een gebeurtenis in de audit logboeken die **30 dagen** worden bewaard. U kunt deze logboeken bekijken in de portal of downloaden als CSV-bestand.

Gebruik de volgende procedure om aan de slag te gaan met Azure AD-audit logboeken:

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer een gebruiks voorwaarden.
1. Klik op **Auditlogboeken weergeven**.

   ![Gebruiksvoorwaarden Blade met de optie controle logboeken weer geven gemarkeerd](./media/terms-of-use/audit-tou.png)

1. In het scherm controle logboeken van Azure AD kunt u de gegevens filteren met behulp van de opgegeven lijsten om specifieke controle logboek gegevens te richten.

   U kunt ook op **Downloaden** klikken om de informatie te downloaden naar een CSV-bestand voor lokaal gebruik.

   ![Controle logboeken van Azure AD scherm datum, doel beleid, geïnitieerd door en activiteit](./media/terms-of-use/audit-logs-tou.png)

   Als u op een logboek klikt, verschijnt er een deel venster met aanvullende details van de activiteit.

   ![Activiteit gegevens voor een logboek met activiteiten, activiteitsstatus, geïnitieerd door, doel beleid](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Welke gebruiks voorwaarden zijn er voor gebruikers?

Zodra een gebruiks voorwaarden is gemaakt en afgedwongen, zien gebruikers, die binnen het bereik vallen, het volgende scherm tijdens het aanmelden.

![Voor beeld van gebruiks voorwaarden die worden weer gegeven wanneer een gebruiker zich aanmeldt](./media/terms-of-use/user-tou.png)

Gebruikers kunnen de gebruiks voorwaarden bekijken en, indien nodig, knoppen gebruiken om in en uit te zoomen.

![Weer gave van gebruiks voorwaarden met Zoom knoppen](./media/terms-of-use/zoom-buttons.png)

In het volgende scherm ziet u hoe de gebruiks voorwaarden op mobiele apparaten eruitzien.

![Voor beeld van gebruiks voorwaarden die worden weer gegeven wanneer een gebruiker zich aanmeldt op een mobiel apparaat](./media/terms-of-use/mobile-tou.png)

Gebruikers moeten de gebruiks voorwaarden slechts eenmaal accepteren en de gebruiks voorwaarden worden niet meer weer geven voor volgende aanmeldingen.

### <a name="how-users-can-review-their-terms-of-use"></a>Hoe gebruikers hun gebruiks voorwaarden kunnen bekijken

Gebruikers kunnen de gebruiks voorwaarden bekijken en bekijken die ze hebben geaccepteerd met behulp van de volgende procedure.

1. Meld u aan bij [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Klik in de rechter bovenhoek op uw naam en selecteer **profiel**.

   ![MyApps-site met het deel venster van de gebruiker geopend](./media/terms-of-use/tou14.png)

1. Klik in uw profielpagina op **Gebruiksvoorwaarden controleren**.

   ![Profiel pagina voor een gebruiker met de koppeling beoordelings voorwaarden van gebruik](./media/terms-of-use/tou13a.png)

1. Vanaf hier kunt u de gebruiksrechtovereenkomst bekijken die u hebt geaccepteerd.

## <a name="edit-terms-of-use-details"></a>Details van gebruiks voorwaarden bewerken

U kunt enkele details van de gebruiks voorwaarden bewerken, maar niet wijzigen van een bestaand document. In de volgende procedure wordt beschreven hoe u de details kunt bewerken.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer de gebruiks voorwaarden die u wilt bewerken.
1. Klik op **voor waarden bewerken**.
1. In het deel venster gebruiks voorwaarden bewerken kunt u het volgende wijzigen:
     - **Naam** : dit is de interne naam van de gebruiks voorwaarden die niet worden gedeeld met eind gebruikers
     - **Weergave naam** : dit is de naam die eind gebruikers kunnen zien bij het bekijken van de gebruiks voorwaarden
     - **Gebruikers moeten de gebruiks voorwaarden uitvouwen** : als u deze optie instelt **op aan** , wordt het einde van het document uitgevouwen voordat het wordt geaccepteerd.
     - Evaluatie U kunt **een bestaand gebruiks document bijwerken**
     - U kunt een taal toevoegen aan een bestaande gebruiks voorwaarden

   Als er andere instellingen zijn die u wilt wijzigen, zoals een PDF-document, moet u gebruikers toestemming geven op elk apparaat, verlopend en verlopen, duur vóór heracceptatie of beleid voor voorwaardelijke toegang. u kunt ook een nieuwe gebruiks voorwaarden maken.

    ![Bewerken met andere taal opties ](./media/terms-of-use/edit-terms-use.png)

1. Wanneer u klaar bent, klikt u op **Opslaan** om uw wijzigingen op te slaan.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>De versie of het PDF-bestand van een bestaande gebruiks voorwaarden bijwerken

1.  Meld u aan bij Azure en ga naar [Gebruiksvoorwaarden](https://aka.ms/catou)
2.  Selecteer de gebruiks voorwaarden die u wilt bewerken.
3.  Klik op **voor waarden bewerken**.
4.  Voor de taal waarin u een nieuwe versie wilt bijwerken, klikt u op **bijwerken** onder de kolom actie
 
     ![Deel venster met gebruiks voorwaarden bewerken naam en Uitvouw opties weer geven](./media/terms-of-use/edit-terms-use.png)

5.  Upload in het deel venster aan de rechter kant het PDF-bestand voor de nieuwe versie
6.  Er is ook een wissel optie nodig om opnieuw te **accepteren** als u wilt dat uw gebruikers deze nieuwe versie de volgende keer aanmelden. Als u wilt dat uw gebruikers de volgende keer proberen om toegang te krijgen tot de resource die is gedefinieerd in het beleid voor voorwaardelijke toegang, wordt u gevraagd deze nieuwe versie te accepteren. Als uw gebruikers niet opnieuw moeten worden geaccepteerd, blijven de voor gaande toestemming actueel en worden alleen nieuwe gebruikers die nog niet hebben gereageerd of waarvan de toestemming verloopt, de nieuwe versie weer gegeven.

    ![De optie voor het gebruik van de gebruiksrecht overeenkomst bewerken gemarkeerd](./media/terms-of-use/re-accept.png)

7.  Wanneer u de nieuwe PDF hebt geüpload en u hebt besloten op opnieuw accepteren, klikt u onder aan het deel venster op toevoegen.
8.  U ziet nu de meest recente versie in de kolom document.

## <a name="view-previous-versions-of-a-terms-of-use"></a>Eerdere versies van de gebruiks voorwaarden weer geven

1.  Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op https://aka.ms/catou.
2.  Selecteer de gebruiks voorwaarden waarvoor u een versie geschiedenis wilt weer geven.
3.  Klik op **talen en versie geschiedenis**
4.  Klik op **vorige versies weer geven.**

    ![document gegevens inclusief taal versies](./media/terms-of-use/document-details.png)

5.  U kunt op de naam van het document klikken om die versie te downloaden

## <a name="see-who-has-accepted-each-version"></a>Zien wie elke versie heeft geaccepteerd

1.  Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op https://aka.ms/catou.
2.  Als u wilt zien wie de gebruiks voorwaarden momenteel heeft geaccepteerd, klikt u op het nummer in de kolom **geaccepteerd** voor de gewenste gebruiks voorwaarden.
3.  Op de volgende pagina wordt standaard de huidige status weer gegeven van elke gebruiker die de gebruiksrecht overeenkomst aanvaardt
4.  Als u de vorige evenementen voor toestemming wilt zien, kunt u **alle** selecteren in de vervolg keuzelijst **huidige status** . Nu kunt u de gebeurtenissen van elke gebruiker zien in details over elke versie en wat er is gebeurd.
5.  U kunt ook een specifieke versie selecteren in de vervolg keuzelijst **versie**  om te zien wie de specifieke versie heeft geaccepteerd.


## <a name="add-a-terms-of-use-language"></a>Een taal voor de gebruiks voorwaarden toevoegen

In de volgende procedure wordt beschreven hoe u een taal voor de gebruiks voorwaarden kunt toevoegen.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer de gebruiks voorwaarden die u wilt bewerken.
1. Klik op **voor waarden bewerken**
1. Klik onder aan de pagina op **taal toevoegen** .
1. Upload uw gelokaliseerde PDF in het deel venster taal voor de gebruiks voorwaarden toevoegen en selecteer de taal.

   ![Gebruiksvoorwaarden geselecteerd en toont het tabblad talen in het detail venster](./media/terms-of-use/select-language.png)

1. Klik op **taal toevoegen**.
1. Klik op **Opslaan**.

1. Klik op **toevoegen** om de taal toe te voegen.

## <a name="per-device-terms-of-use"></a>Gebruiks voorwaarden per apparaat

Met de optie gebruikers moeten toestemming geven voor **elke Apparaatinstellingen** kunt u eind gebruikers verplichten de gebruiks voorwaarden te accepteren op elk apparaat waartoe ze toegang hebben. De eind gebruiker moet hun apparaat registreren bij Azure AD. Wanneer het apparaat is geregistreerd, wordt de apparaat-ID gebruikt voor het afdwingen van de gebruiks voorwaarden op elk apparaat.

Hier volgt een lijst met de ondersteunde platforms en software.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Anders |
> | --- | --- | --- | --- | --- |
> | **Systeemeigen app** | Ja | Ja | Ja |  |
> | **Microsoft Edge** | Ja | Ja | Ja |  |
> | **Internet Explorer** | Ja | Ja | Ja |  |
> | **Chrome (met uitbrei ding)** | Ja | Ja | Ja |  |

Voor de gebruiks voorwaarden per apparaat gelden de volgende beperkingen:

- Een apparaat kan alleen worden gekoppeld aan één Tenant.
- Een gebruiker moet machtigingen hebben om lid te worden van hun apparaat.
- De intune-inschrijvings-app wordt niet ondersteund. Zorg ervoor dat deze is uitgesloten van het beleid voor voorwaardelijke toegang dat gebruiks voorwaarden vereist.
- Gebruikers van Azure AD B2B worden niet ondersteund.

Als het apparaat van de gebruiker niet is toegevoegd, wordt er een bericht weer gegeven dat ze nodig zijn om lid te worden van hun apparaat. Hun ervaring is afhankelijk van het platform en de software.

### <a name="join-a-windows-10-device"></a>Een apparaat met Windows 10 toevoegen

Als een gebruiker Windows 10 en micro soft Edge gebruikt, wordt er een bericht weer gegeven dat vergelijkbaar is met het volgende, om lid te worden van [hun apparaat](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 en micro soft Edge: berichten die aangeven dat het apparaat moet worden geregistreerd](./media/terms-of-use/per-device-win10-edge.png)

Als ze gebruikmaken van Chrome, wordt u gevraagd om de [uitbrei ding voor Windows 10-accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)te installeren.

### <a name="register-an-ios-device"></a>Een iOS-apparaat registreren

Als een gebruiker een iOS-apparaat gebruikt, wordt u gevraagd om de Microsoft Authenticator- [app](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)te installeren.

### <a name="register-an-android-device"></a>Een Android-apparaat registreren

Als een gebruiker een Android-apparaat gebruikt, wordt u gevraagd om de Microsoft Authenticator- [app](https://play.google.com/store/apps/details?id=com.azure.authenticator)te installeren.

### <a name="browsers"></a>Browsers

Als een gebruiker browser gebruikt die niet wordt ondersteund, wordt u gevraagd een andere browser te gebruiken.

![Bericht dat aangeeft dat uw apparaat moet zijn geregistreerd, maar de browser wordt niet ondersteund](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Gebruiks voorwaarden verwijderen

U kunt oude gebruiks voorwaarden verwijderen met behulp van de volgende procedure.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer de gebruiksrechtovereenkomst die u wilt verwijderen.
1. Klik op **Gebruiksvoorwaarden verwijderen**.
1. Klik in het bericht waarin u wordt gevraagd of u wilt doorgaan op **Ja**.

   ![Bericht dat vraagt om bevestiging voor het verwijderen van de gebruiks voorwaarden](./media/terms-of-use/delete-tou.png)

   Het is niet meer mogelijk om uw gebruiks voorwaarden te zien.

## <a name="deleted-users-and-active-terms-of-use"></a>Verwijderde gebruikers en actieve gebruiks voorwaarden

Standaard blijft een verwijderde gebruiker gedurende 30 dagen in Azure AD aanwezig met de status Verwijderd. In deze periode kan de gebruiker eventueel door een beheerder worden teruggezet. Na dertig dagen wordt die gebruiker definitief verwijderd. Daarnaast kan een globale beheerder met behulp van de Azure Active Directory-Portal expliciet [een onlangs verwijderde gebruiker permanent verwijderen](../fundamentals/active-directory-users-restore.md) voordat die periode wordt bereikt. Een gebruiker is definitief verwijderd. volgende gegevens over die gebruiker worden verwijderd uit de actieve gebruiks voorwaarden. Controle-informatie over verwijderde gebruikers blijft in het auditlogboek aanwezig.

## <a name="policy-changes"></a>Beleidswijzigingen

Beleids regels voor voorwaardelijke toegang worden direct van kracht. Als dit gebeurt, zal de beheerder beginnen met het weer geven van ' verdrietige Clouds ' of ' Azure AD-token problemen '. De beheerder moet zich afmelden en weer aanmelden om te kunnen voldoen aan het nieuwe beleid.

> [!IMPORTANT]
> Gebruikers in het bereik moeten zich af- en aanmelden om te voldoen aan een nieuw beleid:
>
> - een beleid voor voorwaardelijke toegang is ingeschakeld voor de gebruiks voorwaarden
> - of als er een tweede gebruiksrechtovereenkomst is gemaakt

## <a name="b2b-guests"></a>B2B-gasten

De meeste organisaties beschikken over een proces voor hun werk nemers om toestemming te geven voor de gebruiks voorwaarden en privacyverklaringen van de organisatie. Maar hoe kunt u dezelfde toestemmingen afdwingen voor gasten van Azure AD Business-to-Business (B2B) wanneer ze worden toegevoegd via share point of teams? Door gebruik te maken van voorwaardelijke toegang en gebruiks voorwaarden, kunt u een beleid rechtstreeks afdwingen voor B2B-gast gebruikers. Tijdens de inwissel van de uitnodiging krijgt de gebruiker de gebruiks voorwaarden te zien. Deze ondersteuning is momenteel beschikbaar als preview-versie.

Gebruiksvoorwaarden wordt alleen weer gegeven wanneer de gebruiker een gast account in azure AD heeft. Share point online heeft momenteel een [ad hoc externe ontvanger](/sharepoint/what-s-new-in-sharing-in-targeted-release) voor het delen van een document of een map waarvoor de gebruiker geen gast account nodig heeft. In dit geval wordt er geen gebruiks voorwaarden weer gegeven.

![Het deel venster gebruikers en groepen-tabblad includes met alle gast gebruikers opties ingeschakeld](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Ondersteuning voor Cloud-apps

Gebruiksvoorwaarden kunnen worden gebruikt voor verschillende Cloud-apps, zoals Azure Information Protection en Microsoft Intune. Deze ondersteuning is momenteel beschikbaar als preview-versie.

### <a name="azure-information-protection"></a>Azure Information Protection

U kunt een beleid voor voorwaardelijke toegang configureren voor de app Azure Information Protection en vereisen een gebruiks voorwaarden wanneer een gebruiker toegang heeft tot een beveiligd document. Hiermee wordt een gebruiks voorwaarden geactiveerd voor de eerste keer dat een gebruiker toegang heeft tot een beveiligd document.

![Deel venster Cloud-apps met Microsoft Azure Information Protection app geselecteerd](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Inschrijving Microsoft Intune

U kunt een beleid voor voorwaardelijke toegang configureren voor de Microsoft Intune-inschrijvings-app en een gebruiks voorwaarden vereisen vóór de inschrijving van een apparaat in intune. Zie voor meer informatie de optie Lees [de oplossing juiste voor waarden voor uw organisatie blog post](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Deel venster Cloud-apps met Microsoft Intune app geselecteerd](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> De intune-inschrijvings-app wordt niet ondersteund voor de [gebruiks voorwaarden per apparaat](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Hoe kan ik zien wanneer/als een gebruiker een gebruiksrecht overeenkomst heeft geaccepteerd?**<br />
A: Klik op de Blade Gebruiksvoorwaarden op het aantal onder **geaccepteerd**. U kunt ook de accepteren-activiteit weer geven of doorzoeken in de Azure AD-controle Logboeken. Zie rapport weer geven van wie is geaccepteerd en geweigerd en [Azure AD-controle logboeken weer geven](#view-azure-ad-audit-logs)voor meer informatie.

**V: hoe lang worden gegevens opgeslagen?**<br />
A: de gebruiker telt in het rapport met gebruiks voorwaarden en die is geaccepteerd/geweigerd voor de levens duur van de gebruiks voorwaarden. De Azure AD-controle logboeken worden 30 dagen bewaard.

**V: Waarom zie ik een ander aantal toestemmingen in het rapport met gebruiks voorwaarden versus de Azure AD-audit logboeken?**<br />
A: het rapport met gebruiks voorwaarden wordt opgeslagen voor de levens duur van die gebruiks voorwaarden, terwijl de Azure AD-audit logboeken 30 dagen worden bewaard. Daarnaast worden in het rapport voor de gebruiks voorwaarden alleen de gebruikers status van huidige toestemming weer gegeven. Als een gebruiker bijvoorbeeld weigert en vervolgens accepteert, wordt in het rapport met gebruiks voorwaarden alleen de acceptatie van die gebruiker weer gegeven. Als u de geschiedenis wilt zien, kunt u de Azure AD-controle Logboeken gebruiken.

**V: als ik de Details voor een gebruiks voorwaarden Bewerk, moeten gebruikers het opnieuw accepteren?**<br />
A: Nee, als een beheerder de Details voor de gebruiks voorwaarden (naam, weergave naam, gebruikers verplicht stellen om uit te breiden of een taal toe te voegen), is het niet vereist dat gebruikers de nieuwe voor waarden accepteren.

**V: kan ik een bestaand gebruiks document van de gebruiksrecht overeenkomst bijwerken?**<br />
A: op dit moment kunt u een bestaand gebruiks document niet bijwerken. Als u een document met de gebruiksrecht overeenkomst wilt wijzigen, moet u een nieuw exemplaar van de gebruiksrecht overeenkomst maken.

**V: als Hyper links in het PDF-document met voor waarden worden gebruikt, kunnen eind gebruikers er op klikken?**<br />
A: Ja, eind gebruikers kunnen hyper links selecteren naar extra pagina's, maar koppelingen naar secties binnen het document worden niet ondersteund. Hyper links in de context van het gebruik van Pdf's werken ook niet wanneer ze worden geopend vanuit de Azure AD MyApps/MyAccount-Portal.

**V: kan een gebruiks voorwaarde gebruikmaken van meerdere talen?**<br />
A: Ja. Momenteel zijn er 108 verschillende talen die een beheerder kan configureren voor een enkele gebruiks voorwaarden. Een beheerder kan meerdere PDF-documenten uploaden en deze documenten labelen met een bijbehorende taal (Maxi maal 108). Wanneer eind gebruikers zich aanmelden, bekijken we de voor keuren van de browser taal en worden ze het overeenkomende document weer gegeven. Als er geen overeenkomst wordt gevonden, wordt het standaard document weer gegeven. Dit is het eerste document dat wordt geüpload.

**V: wanneer zijn de gebruiks voorwaarden geactiveerd?**<br />
A: De gebruiksrechtovereenkomst wordt geactiveerd tijdens het aanmelden.

**V: Voor welke toepassingen kan ik een gebruiksrechtovereenkomst implementeren?**<br />
A: u kunt een beleid voor voorwaardelijke toegang maken voor de bedrijfs toepassingen met moderne verificatie. Zie [Bedrijfstoepassingen](./../manage-apps/view-applications-portal.md) voor meer informatie.

**V: kan ik meerdere gebruiks voorwaarden toevoegen aan een bepaalde gebruiker of app?**<br />
A: Ja, door meerdere beleids regels voor voorwaardelijke toegang te maken die zijn gericht op deze groepen of toepassingen. Als een gebruiker binnen het bereik van meerdere gebruiks voorwaarden valt, accepteren ze één gebruiks voorwaarden tegelijk.

**V: Wat gebeurt er als een gebruiker de gebruiksrechtovereenkomst afwijst?**<br />
A: De gebruiker krijgt dan geen toegang tot de toepassing. De gebruiker moet zich opnieuw aanmelden en de voor waarden accepteren om toegang te krijgen.

**V: is het mogelijk om een eerder geaccepteerde gebruiksrecht overeenkomst te accepteren?**<br />
A: u kunt [eerder geaccepteerde gebruiks voorwaarden bekijken](#how-users-can-review-their-terms-of-use), maar momenteel is er geen manier om te accepteren.

**V: wat gebeurt er als ik ook intune-voor waarden gebruik?**<br />
A: als u zowel Azure AD-gebruiks voorwaarden als [intune](/intune/terms-and-conditions-create)-voor waarden hebt geconfigureerd, is de gebruiker verplicht beide te accepteren. Voor meer informatie, zie de [keuze van de oplossing juiste voor waarden voor uw organisatie blog post](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**V: welke eind punten worden gebruikt voor de voor waarden van het gebruik van de service voor verificatie?**<br />
A: Gebruiksvoorwaarden gebruikt de volgende eind punten voor verificatie: https://tokenprovider.termsofuse.identitygovernance.azure.com en https://account.activedirectory.windowsazure.com . Als uw organisatie een lijst met Url's voor inschrijving heeft, moet u deze eind punten toevoegen aan de acceptatie lijst, samen met de Azure AD-eind punten voor aanmelding.

## <a name="next-steps"></a>Volgende stappen

- [Quickstart: Vereisen dat gebruiksvoorwaarden worden geaccepteerd voor toegang tot cloud-apps](require-tou.md)