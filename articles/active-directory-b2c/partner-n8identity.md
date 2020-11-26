---
title: Zelf studie voor het configureren van de N8-identiteit met Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Zelf studie voor het configureren van het TheAccessHub-beheer programma met Azure Active Directory B2C voor het adresseren van de migratie van klanten accounts en klantenservice aanvragen (CSR).
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 337275cef0f2159cb5fac40ac0435408baf3bbef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170919"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van TheAccessHub-beheer hulpprogramma met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het integreren van Azure Active Directory (AD) B2C met [TheAccessHub-beheer hulpprogramma](https://n8id.com/products/theaccesshub-admintool/) van N8-identiteit. Deze oplossing is gericht op het beheer van de migratie van klanten accounts en klantenservice aanvragen (CSR).  

Deze oplossing is geschikt voor u als u een of meer van de volgende behoeften hebt:

- U hebt een bestaande site en u wilt migreren naar Azure AD B2C. U bent echter lastig met de migratie van uw klant accounts, inclusief wacht woorden

- U hebt een hulp programma voor uw CSR nodig om Azure AD B2C accounts te beheren.

- U hebt een vereiste voor het gebruik van gedelegeerd beheer voor uw Csr's.

- U wilt uw gegevens synchroniseren en samen voegen van vele opslag plaatsen in Azure AD B2C.

## <a name="pre-requisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- Een [Azure AD B2C-Tenant](./tutorial-create-tenant.md). De Tenant moet worden gekoppeld aan uw Azure-abonnement.

- Een TheAccessHub-beheer hulpprogramma omgeving: Neem contact op met [N8-identiteit](https://n8id.com/contact/) om een nieuwe omgeving in te richten.

- Beschrijving Verbindings-en referentie gegevens voor alle data bases of het LDAPs (Lightweight Directory Access Protocol) waarvan u de klant gegevens wilt migreren.

- Beschrijving Geconfigureerde Azure AD B2C-omgeving voor het gebruik van [aangepast beleid](./custom-policy-get-started.md), als u het TheAccessHub-beheer programma wilt integreren in uw beleids stroom voor registratie.

## <a name="scenario-description"></a>Scenariobeschrijving

Het TheAccessHub-beheer programma wordt uitgevoerd, zoals elke andere toepassing in Azure. Het kan worden uitgevoerd in het Azure-abonnement van de N8-identiteit of op het abonnement van de klant. In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Afbeelding met n8identity-architectuur diagram](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt op een aanmeldings pagina. Gebruikers selecteren registreren om een nieuw account te maken en gegevens in te voeren op de pagina. Azure AD B2C worden de gebruikers kenmerken verzameld.
| 2. | Azure AD B2C roept het TheAccessHub-beheer hulpprogramma aan en geeft de gebruikers kenmerken door
| 3. | TheAccessHub-beheer hulpprogramma controleert uw bestaande Data Base op huidige gebruikers gegevens.  
| 4. | De gebruikers record is gesynchroniseerd vanuit de Data Base naar het beheer programma TheAccessHub.
| 5. | Het hulp programma TheAccessHub-beheer deelt de gegevens met de gedelegeerde CSR/helpdesk beheerder.
| 6. | Het TheAccessHub-beheer hulpprogramma synchroniseert de gebruikers records met Azure AD B2C.
| 7. |Op basis van het antwoord op geslaagde/mislukte pogingen van het TheAccessHub-beheer programma, stuurt Azure AD B2C een aangepaste welkomst-e-mail naar de gebruiker.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Een globale beheerder maken in uw Azure AD B2C-Tenant

Voor het TheAccessHub-beheer programma zijn machtigingen vereist om namens een globale beheerder te handelen om gebruikers gegevens te lezen en wijzigingen in uw Azure AD B2C-Tenant uit te voeren. Wijzigingen aan uw vaste beheerders gewonnen; de impact van de TheAccessHub-beheer Hulpprogramma's voor de interactie met de Tenant.

Als u een globale beheerder wilt maken, voert u de volgende stappen uit:

1. Meld u in de Azure Portal aan bij uw Azure AD B2C Tenant als beheerder. Ga naar **Azure Active Directory**  >  **gebruikers**
2. **Nieuwe gebruiker** selecteren
3. Kies **gebruiker maken** om een gewone Directory gebruiker en geen klant te maken
4. Het formulier identiteits gegevens volt ooien

   a. Voer de gebruikers naam in, zoals ' theaccesshub '

   b. Voer de naam in, zoals ' TheAccessHub-service account '

5. Selecteer **wacht woord weer geven** en het eerste wacht woord kopiëren voor later gebruik
6. De rol van globale beheerder toewijzen

   a. Selecteer de **gebruiker** van de huidige rol van de gebruiker om deze te wijzigen

   b. Controleer de record voor de globale beheerder

   c. **Selecteer**  >  **Maken**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Verbinding maken met TheAccessHub-beheer hulpprogramma met uw Azure AD B2C-Tenant

Het TheAccessHub-beheer programma maakt gebruik van de Graph API van micro soft om uw directory te lezen en wijzigingen aan te brengen. Het fungeert als een globale beheerder in uw Tenant. Er is extra machtiging nodig voor het TheAccessHub-beheer programma, dat u vanuit het hulp programma kunt verlenen.

Voer de volgende stappen uit om het TheAccessHub-beheer hulpprogramma te machtigen om toegang te krijgen tot uw Directory:

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die u hebt gekregen door N8-identiteit

2. Ga naar **systeem beheer**  >  **Azure AD B2C configuratie**

3. **Verbinding autoriseren** selecteren

4. Meld u in het nieuwe venster aan met het account van uw globale beheerder. U wordt mogelijk gevraagd om uw wacht woord opnieuw in te stellen als u zich voor de eerste keer aanmeldt met het nieuwe service account.

5. Volg de aanwijzingen en selecteer **accepteren** om TheAccessHub-beheer hulpprogramma de aangevraagde machtigingen te verlenen.

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>Een nieuwe CSR-gebruiker configureren met uw bedrijfs identiteit

Maak een CSR/helpdesk gebruiker die toegang heeft tot het TheAccessHub-beheer hulpprogramma met hun bestaande referenties voor ondernemings Azure Active Directory.

Als u de CSR/helpdesk gebruiker wilt configureren met eenmalige aanmelding (SSO), wordt het aanbevolen om de volgende stappen uit te voeren:

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die zijn verschaft door de N8-identiteit.

2. Navigeren naar **beheer tools**  >  **collega's beheren**

3. **Collega toevoegen** selecteren

4. **Type collega selecteren Azure-beheerder**

5. De profiel gegevens van de collega invoeren

   a. Als u een thuis organisatie kiest, bepaalt u wie er gemachtigd is om deze gebruiker te beheren.

   b. Geef voor aanmeldings-ID/Azure AD-gebruikers naam de principal-naam van de gebruiker op uit het Azure Active Directory account van de gebruiker.

   c. Schakel op het tabblad TheAccessHub roles de Help Desk van de beheerde rol in. Hiermee krijgt de gebruiker toegang tot de weer gave collega's beheren. De gebruiker moet zich nog steeds in een groep bevinden of de eigenaar van de organisatie in staat stelt om op klanten te reageren.

6. Selecteer **Indienen**.

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>Een nieuwe CSR-gebruiker configureren met een nieuwe identiteit

Maak een CSR/helpdesk gebruiker die toegang heeft tot het TheAccessHub-beheer programma met een nieuwe lokale referentie die uniek is voor TheAccessHub-beheer programma. Dit wordt hoofd zakelijk gebruikt door organisaties die geen Azure AD gebruiken voor hun onderneming.

Als u [een CSR/helpdesk](https://youtu.be/iOpOI2OpnLI) gebruiker zonder SSO wilt instellen, voert u de volgende stappen uit:

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die zijn verschaft door de N8-identiteit

2. Navigeren naar **beheer tools**  >  **collega's beheren**

3. **Collega toevoegen** selecteren

4. **Type collega selecteren lokale beheerder**

5. De profiel gegevens van de collega invoeren

   a. Als u een thuis organisatie kiest, bepaalt u wie er gemachtigd is om deze gebruiker te beheren.

   b. Selecteer op het tabblad **TheAccessHub rollen** de **Help Desk** van de beheerde rol. Hiermee krijgt de gebruiker toegang tot de weer gave collega's beheren. De gebruiker moet zich nog steeds in een groep bevinden of de eigenaar van de organisatie in staat stelt om op klanten te reageren.

6. Kopieer de **aanmeldings-id/het e-mail adres** en de **eenmalige wachtwoord** kenmerken. Bied het aan de nieuwe gebruiker. Deze referenties worden gebruikt om u aan te melden bij het TheAccessHub-beheer programma. De gebruiker wordt gevraagd om een nieuw wacht woord in te voeren bij de eerste aanmelding.

7. Selecteer **verzenden**

## <a name="configure-partitioned-csr-administration"></a>Beheer van gepartitioneerde CSR configureren

Machtigingen voor het beheren van de klant en CSR/helpdesk gebruikers in het hulp programma TheAccessHub-beheer worden beheerd met het gebruik van een organisatie hiërarchie. Alle collega's en klanten hebben een thuis organisatie waar ze zich bevinden. Specifieke collega's of groepen collega's kunnen worden toegewezen als eigen aren van organisaties.  Organisatie-eigen aren kunnen collega's en klanten beheren (wijzigingen aanbrengen in) in organisaties of suborganisaties. Als u meerdere collega's wilt toestaan een set gebruikers te beheren, kunt u een groep maken met veel leden. De groep kan vervolgens worden toegewezen als organisatie-eigenaar en alle leden van de groep kunnen collega's en klanten in de organisatie beheren.

### <a name="create-a-new-group"></a>Een nieuwe groep maken

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de **referenties** die u hebt gekregen door N8-identiteit

2. Navigeren naar **organisatie > groepen beheren**

3. Selecteer > **groep toevoegen**

4. Voer een **groeps naam**, **groeps beschrijving** en **groeps eigenaar** in

5. Zoek en schakel de selectie vakjes in van de collega's die u lid wilt maken van de groep en selecteer vervolgens >**toevoegen**

6. Aan de onderkant van de pagina ziet u alle leden van de groep.

7. Als de benodigde leden kunnen worden verwijderd door de **x** aan het einde van de rij te selecteren

8. Selecteer **verzenden**

### <a name="create-a-new-organization"></a>Een nieuwe organisatie maken

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die u hebt gekregen door N8-identiteit

2. Ga naar organisatie > **organisaties te beheren**

3. Selecteer > **organisatie toevoegen**

4. Geef de **naam** van de organisatie, de **eigenaar** van de organisatie en de **bovenliggende organisatie** op.

    a. De naam van de organisatie is in het ideale geval een waarde die overeenkomt met uw klant gegevens. Als u de naam van de organisatie in de werk belasting opgeeft, kan de collega automatisch in de organisatie worden geplaatst bij het laden van de gegevens van de collega en de klant.

    b. De eigenaar vertegenwoordigt de persoon of groep die de klanten en collega's in deze organisatie en een wille keurige suborganisatie gaat beheren.

    c. De bovenliggende organisatie geeft aan welke andere organisatie inherent is, ook verantwoordelijk voor deze organisatie.

5. Selecteer **Indienen**.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>De hiërarchie wijzigen via de structuur weergave

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die u hebt gekregen door N8-identiteit

2. Navigeren naar **Manager Tools** de  >  **structuur weergave** van de Manager-hulpprogram ma's

3. In deze representatie kunt u visualiseren welke collega's en groepen kunnen beheren welke organisaties.

4. U kunt de hiërarchieën wijzigen door organisaties te slepen overtop organisaties waarvan u wilt dat ze worden geparenteerd door.

5. Selecteer **Opslaan** wanneer u klaar bent met het wijzigen van de hiërarchie.

## <a name="customize-welcome-notification"></a>Welkomst melding aanpassen

Terwijl u TheAccessHub gebruikt om gebruikers te migreren van een vorige verificatie oplossing naar Azure AD B2C, kunt u de Welkom melding van de gebruiker aanpassen, die tijdens de migratie naar de gebruiker wordt verzonden door TheAccessHub. Dit bericht bevat normaal gesp roken de koppeling voor de klant om een nieuw wacht woord in te stellen in de Azure AD B2C Directory.

De melding aanpassen:

1. Meld u aan bij TheAccessHub met de referenties die u hebt gekregen door N8-identiteit

2. Navigeren naar **systeem beheerders**  >  **meldingen**

3. De sjabloon voor het maken van een **collega** selecteren

4. Selecteer **bewerken**

5. Wijzig zo nodig de velden bericht en sjabloon. Het veld sjabloon is HTML-compatibel en kan meldingen in HTML-indeling verzenden naar e-mail berichten van klanten.

6. Selecteer **Opslaan** wanneer u klaar bent.

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Gegevens van externe gegevens bronnen migreren naar Azure AD B2C

Met het hulp programma TheAccessHub-beheer kunt u gegevens importeren uit verschillende data bases, LDAPs en CSV-bestanden en vervolgens de gegevens naar uw Azure AD B2C-Tenant pushen. Dit wordt gedaan door het laden van gegevens naar het Azure AD B2C type gebruiker in het TheAccessHub-beheer programma.  Als de bron van de gegevens niet Azure zelf is, worden de gegevens in het TheAccessHub-beheer hulpprogramma en Azure AD B2C geplaatst. Als de bron van uw externe gegevens geen eenvoudig CSV-bestand op uw computer is, stelt u een gegevens bron in voordat u de gegevens laadt. In de onderstaande stappen wordt beschreven hoe u een gegevens bron maakt en de gegevens laadt.

### <a name="configure-a-new-data-source"></a>Een nieuwe gegevens bron configureren

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die u hebt gekregen door N8-identiteit

2. Navigeren naar **System Admin**  >  **gegevens bronnen** van systeem beheerder

3. Selecteer **gegevens bron toevoegen**

4. Geef een **naam** en **type** op voor deze gegevens bron

5. Vul de formulier gegevens in, afhankelijk van het type gegevens Bron:

   **Voor data bases**

   a. **Type** – data base

   b. **Database type** : Selecteer een data base in een van de ondersteunde typen data bases.

   c. **Verbindings-URL** : Voer een goed opgemaakte JDBC-Connection String in. Zoals: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Gebruikers naam** : Voer de gebruikers naam in voor toegang tot de data base

   e. **Wacht woord** : Voer het wacht woord in voor toegang tot de data base

   f. **Query** : Voer de SQL-query in om de klant gegevens op te halen. Zoals: ``SELECT * FROM mytable;``

   g. Selecteer **verbinding testen**. er wordt een voor beeld van uw gegevens weer gegeven om te controleren of de verbinding werkt.

   **Voor LDAPs**

   a. **Type** – LDAP

   b. **Host** : Voer de hostnaam of het IP-adres in voor de computer waarop de LDAP-server wordt uitgevoerd. Zoals: ``mysite.com``

   c. **Poort** : Voer het poort nummer in waarin de LDAP-server luistert.

   d. **SSL** : Schakel het selectie vakje in als het TheAccessHub-beheer programma moet communiceren met de LDAP veilig met SSL. Het gebruik van SSL wordt sterk aanbevolen.

   e. **Aanmeldings-DN** : Voer de DN-naam van het gebruikers account in om u aan te melden en de LDAP-zoek opdracht uit te voeren

   f. **Wacht woord** : Voer het wacht woord voor de gebruiker in

   g. **Basis-DN** : Voer de DN in boven de hiërarchie waarin u de zoek actie wilt uitvoeren

   h. **Filteren** : Voer de LDAP-filter teken reeks in waarmee uw klant records worden opgehaald

   i. **Kenmerken** : Voer een door komma's gescheiden lijst met kenmerken van uw klant records in om door te geven aan TheAccessHub-beheer programma

   j. Selecteer de **Test verbinding**. er wordt een voor beeld van uw gegevens weer gegeven om te controleren of de verbinding werkt.

   **Voor OneDrive**

   a. **Type** : OneDrive voor bedrijven

   b. **Verbinding autoriseren** selecteren

   c. Er wordt een nieuw venster weer gegeven waarin u wordt gevraagd om u aan te melden bij **OneDrive**. Meld u aan met een gebruiker met lees toegang tot uw OneDrive-account. Het TheAccessHub-beheer programma, wordt voor deze gebruiker gereageerd op het lezen van CSV-Laad bestanden.

   d. Volg de aanwijzingen en selecteer **accepteren** om TheAccessHub-beheer hulpprogramma de aangevraagde machtigingen te verlenen.

6. Selecteer **Opslaan** wanneer u klaar bent.  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Gegevens uit uw gegevens bron synchroniseren met Azure AD B2C

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die u hebt gekregen door N8-identiteit

2. Navigeren naar **systeem beheerder**  >  **gegevens synchronisatie**

3. **Nieuwe belasting** selecteren

4. Het **type collega** selecteren Azure AD B2C gebruiker

5. Selecteer **bron**, Selecteer in het pop-updialoogvenster uw gegevens bron. Als u een OneDrive-gegevens bron hebt gemaakt, moet u ook het bestand selecteren.

6. Als u geen nieuwe klanten accounts met deze belasting wilt maken, wijzigt u vervolgens het eerste beleid: **als de collega niet in TheAccessHub is gevonden** , kunt u **niets doen**

7. Als u bestaande klant accounts met deze belasting niet wilt bijwerken, **wijzigt u het** tweede beleid als de gegevens in de **bron-en TheAccessHub niet overeenkomen** .

8. Selecteer **Volgende**

9. In de configuratie voor het toewijzen van de **Zoek opdracht** wordt aangegeven hoe u belasting records met klanten die al in TheAccessHub-beheer programma zijn geladen, kunt correleren. Kies een of meer identificerende kenmerken in de bron. Vergelijk de kenmerken met een kenmerk in het TheAccessHub-beheer hulpprogramma dat dezelfde waarden bevat. Als er een overeenkomst wordt gevonden, wordt de bestaande record genegeerd. anders wordt een nieuwe klant gemaakt. U kunt een aantal van deze controles sequentieel. U kunt bijvoorbeeld eerst de e-mail controleren, en vervolgens de voor-en achternaam.

10. Selecteer **gegevens toewijzing** in het menu aan de linkerkant.

11. Wijs in de Data-Mapping configuratie aan welke kenmerken van het TheAccessHub-beheer hulpprogramma moeten worden ingevuld op basis van de bron kenmerken. U hoeft niet alle kenmerken toe te wijzen. Niet-toegewezen kenmerken blijven ongewijzigd voor bestaande klanten.

12. Als u toewijst aan het kenmerk org_name met een waarde die de naam van een bestaande organisatie is, worden nieuwe klanten gemaakt in die organisatie.

13. Selecteer **Volgende**

14. Als deze belasting opnieuw moet worden uitgevoerd, kan een dagelijks/wekelijks of maandelijks schema worden opgegeven. Zo niet, dan wordt de standaard waarde **nu** bewaard.

15. Selecteer **verzenden**

16. Als het **schema nu** is geselecteerd, wordt direct een nieuwe record toegevoegd aan het scherm gegevens synchronisaties. Zodra de validatie fase 100% heeft bereikt, selecteert u de **nieuwe record** om het verwachte resultaat voor de belasting weer te geven. Voor geplande belastingen worden deze records alleen weer gegeven na de geplande tijd.

17. Als er geen fouten zijn, selecteert u **uitvoeren** om de wijzigingen door te voeren. Als dat niet het geval is, selecteert u **verwijderen** in het menu **meer** om de belasting te verwijderen. U kunt vervolgens de bron gegevens of het laden van toewijzingen corrigeren en het opnieuw proberen. Als het aantal fouten klein is, kunt u de records hand matig bijwerken en op elke record **bijwerken** selecteren om correcties aan te brengen. Ten slotte kunt u door gaan met eventuele fouten en deze later oplossen als **ondersteunende interventies** in het TheAccessHub-beheer programma.

18. Wanneer de **gegevens synchronisatie** record in de laad fase 100% wordt, zijn alle wijzigingen die zijn ontstaan ten gevolge van de belasting, geïnitieerd. Klanten moeten beginnen met het weer gegeven of ontvangen van wijzigingen in Azure AD B2C.

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Azure AD B2C klant gegevens synchroniseren 

Als eenmalige of lopende bewerking, kan TheAccessHub-beheer programma alle klant gegevens van Azure AD B2C synchroniseren in TheAccessHub-beheer programma. Dit zorgt ervoor dat CSR/helpdesk beheerders actuele klant gegevens bekijken.

Gegevens van Azure AD B2C synchroniseren met TheAccessHub-beheer programma:

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die u hebt gekregen door N8-identiteit

2. Navigeren naar **systeem beheerder**  >  **gegevens synchronisatie**

3. **Nieuwe belasting** selecteren

4. Het **type collega** selecteren Azure AD B2C gebruiker

5. Voor de stap **Opties** , behoud de standaard waarden.

6. Selecteer **Volgende**

7. Voor de **Zoek stap voor gegevens toewijzing &** , moet u de standaard waarden behouden. Behalve als u toewijst aan het kenmerk **org_name** met een waarde die de naam van een bestaande organisatie is, worden nieuwe klanten gemaakt in die organisatie.

8. Selecteer **Volgende**

9. Als deze belasting opnieuw moet worden uitgevoerd, kan een dagelijks/wekelijks of maandelijks schema worden opgegeven. Bewaar anders de standaard instelling: **nu**. U wordt aangeraden om regel matig een synchronisatie uit te voeren op basis van Azure AD B2C.

10. Selecteer **verzenden**

11. Als het schema **nu** is geselecteerd, wordt direct een nieuwe record toegevoegd aan het scherm gegevens synchronisaties. Zodra de validatie fase 100% heeft bereikt, selecteert u de nieuwe record om het verwachte resultaat voor de belasting weer te geven. Voor geplande belastingen worden deze records alleen weer gegeven na de geplande tijd.

12. Als er geen fouten zijn, selecteert u **uitvoeren** om de wijzigingen door te voeren. Als dat niet het geval is, selecteert u **verwijderen** in het menu meer om de belasting te verwijderen. U kunt vervolgens de bron gegevens of het laden van toewijzingen corrigeren en het opnieuw proberen. Als het aantal fouten klein is, kunt u de records hand matig bijwerken en op elke record **bijwerken** selecteren om correcties aan te brengen. Ten slotte kunt u door gaan met eventuele fouten en deze later oplossen als ondersteunende interventies in het TheAccessHub-beheer programma.

13. Wanneer de **gegevens synchronisatie** record in de laad fase 100% wordt, zijn alle wijzigingen die zijn ontstaan ten gevolge van de belasting, geïnitieerd.

## <a name="configure-azure-ad-b2c-policies"></a>Azure AD B2C-beleid configureren

Het TheAccessHub-beheer programma wordt af en toe gesynchroniseerd met een beperkt aantal mogelijkheden om de status up-to-date te houden met Azure AD B2C. We kunnen gebruikmaken van het API-en Azure AD B2C-beleid van het TheAccessHub-beheer hulpprogramma om het TheAccessHub-beheer programma van wijzigingen op de hoogte te stellen wanneer ze plaatsvinden. Deze oplossing vereist technische kennis van [Azure AD B2C aangepast beleid](./custom-policy-get-started.md). In het volgende gedeelte krijgt u een voor beeld van de beleids stappen en een beveiligd certificaat om het TheAccessHub-beheer programma van nieuwe accounts in uw Sign-Up aangepast beleid te melden.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>Een beveiligde referentie maken voor het aanroepen van de API van het TheAccessHub-beheer programma

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die u hebt gekregen door N8-identiteit

2. Ga naar de **systeem beheerder**  >  **beheer hulpprogramma's**  >  **API Security**

3. Selecteer **genereren**

4. Het **certificaat wachtwoord** kopiëren

5. Selecteer **downloaden** om het client certificaat op te halen.

6. Volg deze [zelf studie](./secure-rest-api.md#https-client-certificate-authentication ) om het client certificaat toe te voegen aan Azure AD B2C.

### <a name="retrieve-your-custom-policy-examples"></a>Uw aangepaste beleids voorbeelden ophalen

1. Meld u aan bij TheAccessHub-beheer hulpprogramma met de referenties die u hebt gekregen door N8-identiteit

2. Ga naar beheer hulpprogramma's voor **systeem beheerders**  >  **Admin Tools**  >  **Azure B2C-beleid**

3. Geef uw Azure AD B2C Tenant domein en de twee identiteits experience Framework-Id's op uit uw Framework-configuratie voor identiteits ervaring

4. Selecteer **Opslaan**

5. Selecteer **downloaden** om een zip-bestand op te halen met een basis beleid waarmee klanten worden toegevoegd aan het TheAccessHub-beheer programma als klanten zich registreren.

6. Volg deze [zelf studie](./custom-policy-get-started.md) om aan de slag te gaan met het ontwerpen van aangepast beleid in azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)