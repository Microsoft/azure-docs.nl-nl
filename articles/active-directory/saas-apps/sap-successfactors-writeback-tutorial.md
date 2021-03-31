---
title: 'Zelfstudie: SAP SuccessFactors-write-back configureren in Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van write-back van kenmerken naar SAP SuccessFactors vanuit Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3260787dec4ae26cd6ef7cc3bd562f39db8e3655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526972"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Zelfstudie: Write-back van kenmerken van Azure AD naar SAP SuccessFactors configureren
Het doel van deze zelfstudie is om de stappen te laten zien voor write-back van kenmerken van Azure AD naar SAP SuccessFactors Employee Central. 

## <a name="overview"></a>Overzicht

U kunt de Writeback-app van SAP SuccessFactors configureren voor het schrijven van specifieke kenmerken van Azure Active Directory naar SAP SuccessFactors Employee Central. De inrichtings-app voor write-back van SuccessFactors ondersteunt het toewijzen van waarden aan de volgende centrale kenmerken van Employee Central:

* Werk-e-mailadres
* Gebruikersnaam
* Zakelijk telefoonnummer (inclusief landnummer, netnummer, nummer en toestelnummer)
* Primaire markering van zakelijk telefoonnummer
* Mobiel telefoonnummer (inclusief landnummer, netnummer, nummer)
* Primaire markering van mobiele telefoon 
* Kenmerken custom01-custom15 van gebruiker
* Kenmerk loginMethod

> [!NOTE]
> Deze app is niet afhankelijk van de inrichtingsintegratie-apps voor inkomende gebruikers van SuccessFactors. U kunt deze onafhankelijk van de inrichtings-app voor [SuccessFactors naar on-premises AD](sap-successfactors-inbound-provisioning-tutorial.md) of de inrichtings-app van [SuccessFactors naar Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) configureren.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze oplossing voor het inrichten van gebruikers het meest geschikt?

Deze oplossing voor gebruikersinrichting van SuccessFactors Writeback is het meest geschikt voor:

* Organisaties die Microsoft 365 gebruiken om gezaghebbende kenmerken die door IT worden beheerd (zoals e-mailadres, telefoonnummer, gebruikersnaam), terug te schrijven naar SuccessFactors Employee Central.

## <a name="configuring-successfactors-for-the-integration"></a>SuccessFactors configureren voor de integratie

Alle inrichtingsconnectors van SuccessFactors vereisen referenties van een SuccessFactors-account met de juiste machtigingen om de OData-API's van Employee Central aan te roepen. In deze sectie worden de stappen beschreven voor het maken van het serviceaccount in SuccessFactors en het verlenen van de juiste machtigingen. 

* [API-gebruikersaccount maken/identificeren in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Een API-machtigingenrol maken](#create-an-api-permissions-role)
* [Een machtigingsgroep maken voor de API-gebruiker](#create-a-permission-group-for-the-api-user)
* [Machtigingsrol verlenen aan de machtigingsgroep](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-gebruikersaccount maken/identificeren in SuccessFactors
Werk samen met uw beheerders van SuccessFactors of uw implementatiepartner om een gebruikersaccount in SuccessFactors te maken of te identificeren dat wordt gebruikt om de OData-API's aan te roepen. De gebruikersnaam en het wachtwoord van dit account zijn vereist bij het configureren van de inrichtings-apps in Azure AD. 

### <a name="create-an-api-permissions-role"></a>Een API-machtigingenrol maken

1. Meld u aan bij SAP SuccessFactors met een gebruikersaccount dat toegang heeft tot het Admin Center.
1. Zoek naar *Machtigingsrollen beheren* en selecteer **Machtigingsrollen beheren** in de zoekresultaten.

   ![Machtigingsrollen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Klik in de lijst met machtigingsrollen op **Nieuwe maken**.

   > [!div class="mx-imgBorder"]
   > ![Nieuwe machtigingsrol maken](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Geef waarden op voor **Role Name** en **Description** voor de nieuwe machtigingsrol. De naam en beschrijving moeten aangeven dat de rol bestemd is voor API-gebruiksmachtigingen.

   > [!div class="mx-imgBorder"]
   > ![Details van machtigingsrol](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Klik onder Permission settings op **Permission...** , schuif vervolgens omlaag in de lijst met machtigingen en klik op **Manage Integration Tools**. Schakel het selectievakje van **Allow Admin to Access to OData API through Basic Authentication** in.

   > [!div class="mx-imgBorder"]
   > ![Integratie-hulpprogramma’s beheren](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Schuif omlaag in dezelfde lijst en selecteer **Employee Central-API**. Voeg machtigingen toe, zoals hieronder wordt weergegeven, voor meer informatie over het gebruik van ODATA-API en bewerken met de ODATA-API. Selecteer de optie bewerken als u hetzelfde account wilt gebruiken voor het scenario voor write-back naar SuccessFactors. 

   > [!div class="mx-imgBorder"]
   > ![Machtigingen voor lezen en schrijven](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Klik op **Done**. Klik op **Wijzigingen opslaan**.

### <a name="create-a-permission-group-for-the-api-user"></a>Een machtigingsgroep maken voor de API-gebruiker

1. Zoek in het SuccessFactors Admin Center naar *Manage Permission Groups* en selecteer vervolgens **Manage Permission Groups** in de zoekresultaten.

   > [!div class="mx-imgBorder"]
   > ![Machtigingsgroepen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. Klik in het venster Manage Permission Groups op **Create New**.

   > [!div class="mx-imgBorder"]
   > ![Nieuwe groep toevoegen](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Geef een naam op voor de nieuwe groep. De groepsnaam moet aangeven dat de groep voor API-gebruikers is.

   > [!div class="mx-imgBorder"]
   > ![Naam van machtigingsgroep](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Voeg leden toe aan de groep. U kunt bijvoorbeeld **Username** selecteren in de vervolgkeuzelijst People Pool en vervolgens de gebruikersnaam invoeren van het API-account dat wordt gebruikt voor de integratie. 

   > [!div class="mx-imgBorder"]
   > ![Groepsleden toevoegen](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Klik op **Done** om de machtigingsgroep te maken.

### <a name="grant-permission-role-to-the-permission-group"></a>Machtigingsrol verlenen aan de machtigingsgroep

1. Zoek in het SuccessFactors Admin Center naar *Manage Permission Roles* en selecteer vervolgens **Manage Permission Roles** in de zoekresultaten.
1. Selecteer bij **Permission Role List** de rol die u hebt gemaakt voor de machtigingen voor API-gebruik.
1. Klik onder **Grant this role to...** op de knop **Add...**
1. Selecteer **Permission Group...** in de vervolgkeuzelijst en klik vervolgens op **Select...** om het venster Groups te openen en de hierboven gemaakte groep te selecteren. 

   > [!div class="mx-imgBorder"]
   > ![Machtigingsgroep toevoegen](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Controleer de gegevens voor het verlenen van de machtigingsrol aan de machtigingsgroep. 
   > [!div class="mx-imgBorder"]
   > ![Machtingingsrol en groepsdetails](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Klik op **Wijzigingen opslaan**.

## <a name="preparing-for-successfactors-writeback"></a>Voorbereiden op SuccessFactors Writeback

De inrichtings-app van SuccessFactors Writeback gebruikt bepaalde *code* waarden voor het instellen van e-mail en telefoonnummers in Employee Central. Deze *code* waarden worden ingesteld als constante waarden in de kenmerktoewijzingstabel en zijn voor elk SuccessFactors-exemplaar anders. In deze sectie vindt u de stappen voor het vastleggen van deze *code* waarden.

   > [!NOTE]
   > Zorg dat u uw SuccessFactors-beheerder erbij betrekt om de stappen in deze sectie te voltooien. 

### <a name="identify-email-and-phone-number-picklist-names"></a>Namen van selectielijsten voor E-mail en Telefoonnummers identificeren 

In SAP SuccessFactors is een *selectielijst* een configureerbare set opties waaruit een gebruiker een selectie kan maken. De verschillende soorten e-mailadressen en telefoonnummers (bijvoorbeeld zakelijk, privé, overig) worden vertegenwoordigd door een selectielijst. In deze stap bepalen we de selectielijsten die in de SuccessFactors-tenant zijn geconfigureerd voor het opslaan van de waarden voor e-mailadressen en telefoonnummers. 
 
1. Zoek in het beheercentrum van SuccessFactors naar *Bedrijfsconfiguratie beheren*. 

   > [!div class="mx-imgBorder"]
   > ![Bedrijfsconfiguratie beheren](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. Selecteer onder **HRIS-elementen** **emailInfo** en klik op de *Details* voor het veld **e-mail-type**.

   > [!div class="mx-imgBorder"]
   > ![E-mailgegevens ophalen](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. Noteer op de detailpagina **e-mail-type** de naam van de selectielijst die aan dit veld is gekoppeld. Dit is standaard **ecEmailType**. Het kan echter anders zijn in uw tenant. 

   > [!div class="mx-imgBorder"]
   > ![Selectielijst voor e-mail identificeren](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. Selecteer onder **HRIS-elementen** **phoneInfo** en klik op de *Details* voor het veld **telefoonnummer-type**.

   > [!div class="mx-imgBorder"]
   > ![Telefoonnummergegevens ophalen](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. Noteer op de detailpagina **telefoonnummer-type** de naam van de selectielijst die aan dit veld is gekoppeld. Dit is standaard **ecPhoneType**. Het kan echter anders zijn in uw tenant. 

   > [!div class="mx-imgBorder"]
   > ![Selectielijst voor telefoonnummers identificeren](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>Constante waarde ophalen voor emailType

1. Zoek en open *Selectielijstcentrum* in het beheercentrum van SuccessFactors. 
1. Gebruik de naam van de in de vorige sectie vastgelegde e-mail-selectielijst (bv. ecEmailType) om de e-mail-selectielijst te vinden. 

   > [!div class="mx-imgBorder"]
   > ![Selectielijst voor e-mail-type zoeken](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. Open de selectielijst voor actieve e-mail. 

   > [!div class="mx-imgBorder"]
   > ![Open de selectielijst voor het actieve e-mail-type](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. Selecteer op de pagina selectielijst van e-mail-type het e-mail-type *Zakelijk*.

   > [!div class="mx-imgBorder"]
   > ![Selecteer het e-mail-type zakelijk](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. Noteer de **Optie-ID** die is gekoppeld aan de *Zakelijke* e-mail. Dit is de code die we gaan gebruiken met *emailType* in de kenmerktoewijzingstabel.

   > [!div class="mx-imgBorder"]
   > ![E-mail-typecode ophalen](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > Verwijder het kommateken wanneer u de waarde kopieert. Voorbeeld: als de waarde van **Optie-ID** *8,448* is, stelt u het *emailType* in Azure AD in op het constante nummer *8448* (zonder het kommateken). 

### <a name="retrieve-constant-value-for-phonetype"></a>Constante waarde ophalen voor phoneType

1. Zoek en open *Selectielijstcentrum* in het beheercentrum van SuccessFactors. 
1. Gebruik de naam van de in de vorige sectie vastgelegde telefoonnummer-selectielijst om de telefoonnummer-selectielijst te vinden. 

   > [!div class="mx-imgBorder"]
   > ![Selectielijst voor telefoonnummertype zoeken](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. De actieve selectielijst voor telefoonnummers openen. 

   > [!div class="mx-imgBorder"]
   > ![Open de selectielijst voor het actieve telefoonnummer-type](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. Controleer op de pagina van de selectielijst voor het telefoonnummertype de verschillende typen telefoonnummers onder **Waarden selectielijst**.

   > [!div class="mx-imgBorder"]
   > ![Telefoonnummertypen controleren](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. Noteer de **Optie-ID** die is gekoppeld aan het *Zakelijke* telefoonnummer. Dit is de code die we gaan gebruiken met *businessPhoneType* in de kenmerktoewijzingstabel.

   > [!div class="mx-imgBorder"]
   > ![Zakelijke telefoonnummercode ophalen](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. Noteer de **Optie-ID** die is gekoppeld aan het *Mobiele* telefoonnummer. Dit is de code die we gaan gebruiken met *cellPhoneType* in de kenmerktoewijzingstabel.

   > [!div class="mx-imgBorder"]
   > ![Mobiele telefoonnummercode ophalen](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > Verwijder het kommateken wanneer u de waarde kopieert. Voorbeeld: als de waarde van **Optie-ID** *10,606* is, stelt u het *cellPhoneType* in Azure AD in op het constante nummer *10606* (zonder het kommateken). 


## <a name="configuring-successfactors-writeback-app"></a>De SuccessFactors Writeback-app configureren

Deze sectie bevat stappen voor 

* [De inrichtingsconnector-app toevoegen en connectiviteit configureren voor SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Kenmerktoewijzingen configureren](#part-2-configure-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Deel 1: De inrichtingsconnector-app toevoegen en connectiviteit configureren voor SuccessFactors

**Voor het configureren van SuccessFactors Writeback:**

1. Ga naar <https://portal.azure.com>

2. Selecteer **Azure Active Directory** in de navigatiebalk aan de linkerkant.

3. Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen** en selecteer de categorie **Alle**.

5. Zoek naar **SuccessFactors Writeback** en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weergegeven, selecteert u **Inrichten**

7. Stel **Inrichtings** **modus** in op **Automatisch**.

8. Ga als volgt te werk om de sectie **Referenties voor beheerder** af te ronden:

   * **Gebruikersnaam van beheerder** - Voer de gebruikersnaam van de gebruikersaccount van de SuccessFactors-API in, waarbij de bedrijfs-ID is toegevoegd. Deze heeft de volgende indeling: **gebruikersnaam\@bedrijfsid**

   * **Beheerderswachtwoord -** Voer het wachtwoord in van het gebruikersaccount van de SuccessFactors-API. 

   * **Tenant-URL -** Voer de naam in van het SuccessFactors OData-API-service-eindpunt. Voer alleen de hostnaam van de server in zonder http of https. De waarde moet er als volgt uitzien: `api4.successfactors.com`.

   * **E-mailadres voor meldingen -** Voer uw e-mailadres in en schakel het selectie vakje ‘e-mail verzenden als er een fout is opgetreden’ in.
    > [!NOTE]
    > De Azure AD-inrichtingsservice verzendt een e-mailmelding als de inrichtingstaak in [quarantaine](../app-provisioning/application-provisioning-quarantine-status.md) is geplaatst.

   * Klik op de knop **Verbinding testen**. Als de verbindingstest is gelukt, klikt u bovenaan op de knop **Opslaan**. Als de test mislukt, controleert u nogmaals of de SuccessFactors-referenties en -URL geldig zijn.
    >[!div class="mx-imgBorder"]
    >![Azure-portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Zodra de referenties zijn opgeslagen, wordt de standaardtoewijzing weergegeven in de sectie **Toewijzingen**. Vernieuw de pagina als de kenmerktoewijzingen niet zichtbaar zijn.  

### <a name="part-2-configure-attribute-mappings"></a>Deel 2: Kenmerktoewijzingen configureren

In deze sectie configureert u hoe gebruikersgegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad Inrichten onder **Toewijzingen** op **Azure Active Directory-gebruikers inrichten**.

1. In het veld  **Bereik van bronobject** kunt u selecteren welke sets van gebruikers in Azure AD moeten worden overwogen voor write-back, door een set op kenmerken gebaseerde filters te definiëren. Het standaardbereik is ‘alle gebruikers in Azure AD’. 
   > [!TIP]
   > Wanneer u de inrichtings-app voor de eerste keer configureert, moet u de kenmerktoewijzingen en expressies testen en controleren om ervoor te zorgen dat u het gewenste resultaat krijgt. Microsoft adviseert om de bereikfilters onder **Bereik van bronobject** te gebruiken om uw toewijzingen te testen met een aantal testgebruikers van Azure AD. Wanneer u hebt gecontroleerd of de toewijzingen werken, kunt u het filter verwijderen of het geleidelijk uitbreiden met meer gebruikers.

1. Het veld met **Acties voor het doelobject** ondersteunt alleen de bewerking **Update**.

1. In de toewijzingstabel onder de sectie **Kenmerktoewijzingen** kunt u de volgende Azure Active Directory-kenmerken toewijzen aan SuccessFactors. De volgende tabel bevat richtlijnen voor het toewijzen van de kenmerken voor write-back. 

   | \# | Azure AD-kenmerk | SuccessFactors-kenmerk | Opmerkingen |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | Dit kenmerk is standaard de overeenkomende id. In plaats van employeeId kunt u elk ander Azure AD-kenmerk gebruiken dat de waarde kan opslaan die gelijk is aan personIdExternal in SuccessFactors.    |
   | 2 | mail | e-mail | Bron van e-mailkenmerk toewijzen. Voor testdoeleinden kunt u userPrincipalName toewijzen aan een e-mailadres. |
   | 3 | 8448 | emailType | Deze constante waarde is de SuccessFactors-id-waarde die aan een zakelijk e-mailadres is gekoppeld. Werk deze waarde bij zodat deze overeenkomt met uw SuccessFactors-omgeving. Zie de sectie [Constante waarde ophalen voor emailType](#retrieve-constant-value-for-emailtype) voor stappen om deze waarde in te stellen. |
   | 4 | waar | emailIsPrimary | Gebruik dit kenmerk om zakelijke e-mail in te stellen als primair in SuccessFactors. Als zakelijke e-mail niet primair is, stelt u deze markering in op onwaar. |
   | 5 | userPrincipalName | [custom01 – custom15] | Met **Nieuwe toewijzing toevoegen** kunt u optioneel userPrincipalName of een ander Azure AD-kenmerk schrijven naar een aangepast attribuut dat beschikbaar is in het gebruikersobject van SuccessFactors.  |
   | 6 | On-premises SamAccountName | gebruikersnaam | Met **Nieuwe toewijzing toevoegen** kunt u optioneel een on-premises samAccountName toewijzen aan het gebruikersnaamkenmerk van SuccessFactors. Gebruik [Azure AD Connect Sync: Directory-extensies](../hybrid/how-to-connect-sync-feature-directory-extensions.md) om sAMAccountName te synchroniseren met Azure AD. Deze wordt weer gegeven in de vervolg keuzelijst bron als *extension_yourTenantGUID_samAccountName* |
   | 7 | Eenmalige aanmelding | loginMethod | Als de SuccessFactors-tenant is ingesteld voor [gedeeltelijke eenmalige aanmelding (SSO)](https://apps.support.sap.com/sap/support/knowledge/en/2320766) dan kunt u vervolgens eventueel met Nieuwe toewijzing toevoegen loginMethod instellen op een constante waarde van 'SSO' of 'PW '. |
   | 8 | telephoneNumber | businessPhoneNumber | Gebruik deze toewijzing om *telephoneNumber* van Azure AD naar SuccessFactors zakelijk/werktelefoonnummer te laten stromen. |
   | 9 | 10605 | businessPhoneType | Deze constante waarde is de SuccessFactors-id-waarde die aan een zakelijk telefoonnummer is gekoppeld. Werk deze waarde bij zodat deze overeenkomt met uw SuccessFactors-omgeving. Zie de sectie [Constante waarde ophalen voor phoneType](#retrieve-constant-value-for-phonetype) voor stappen om deze waarde in te stellen. |
   | 10 | waar | businessPhoneIsPrimary | Gebruik dit kenmerk om de primaire markering voor zakelijk telefoonnummer in te stellen. Geldige waarden zijn true (waar) of false (onwaar). |
   | 11 | mobiel | cellPhoneNumber | Gebruik deze toewijzing om *telephoneNumber* van Azure AD naar SuccessFactors zakelijk/werktelefoonnummer te laten stromen. |
   | 12 | 10606 | cellPhoneType | Deze constante waarde is de SuccessFactors-id-waarde die aan een mobiel telefoonnummer is gekoppeld. Werk deze waarde bij zodat deze overeenkomt met uw SuccessFactors-omgeving. Zie de sectie [Constante waarde ophalen voor phoneType](#retrieve-constant-value-for-phonetype) voor stappen om deze waarde in te stellen. |
   | 13 | onjuist | cellPhoneIsPrimary | Gebruik dit kenmerk om de primaire markering voor mobiel telefoonnummer in te stellen. Geldige waarden zijn true (waar) of false (onwaar). |
 
1. Valideer en controleer uw kenmerktoewijzingen. 
 
    >[!div class="mx-imgBorder"]
    >![Toewijzen van write-back-kenmerken](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Klik op **Opslaan** om de toewijzingen op te slaan. Vervolgens worden de API-expressies voor het JSON-pad bijgewerkt om de phoneType-codes in uw SuccessFactors-exemplaar te gebruiken. 
1. Selecteer **Geavanceerde opties weergeven**. 

    >[!div class="mx-imgBorder"]
    >![Geavanceerde opties weergeven](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Klik op **Kenmerkenlijst bewerken voor SuccessFactors**. 

   > [!NOTE] 
   > Als de optie **Kenmerkenlijst bewerken voor SuccessFactors** niet wordt weergegeven in de Azure-portal, gebruikt u de URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* om toegang te krijgen tot de pagina. 

1. In de kolom **API-expressie** in deze weergave worden de JSON-padexpressies weergegeven die worden gebruikt door de connector. 
1. Werk de JSON-padexpressies voor zakelijk telefoonnummer en mobiel telefoonnummer bij om de id-waarde (*businessPhoneType* en *cellPhoneType*) te gebruiken die overeenkomt met uw omgeving. 

    >[!div class="mx-imgBorder"]
    >![Wijziging in het JSON-pad voor telefoonnummer](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Klik op **Opslaan** om de toewijzingen op te slaan.

## <a name="enable-and-launch-user-provisioning"></a>Gebruikersinrichting inschakelen en starten

Zodra de configuratie van de SuccessFactors-inrichtings-app is voltooid, kunt u de inrichtingsservice inschakelen in Azure Portal.

> [!TIP]
> Wanneer u de inrichtingsservice inschakelt, worden er standaard inrichtingsbewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten zijn opgetreden in de toewijzing of problemen zijn met gegevens, kan de inrichtingstaak mislukken en gaat u naar de quarantaine-status. Om dit te voorkomen, raden we u als best practice aan om het filter **Bereik van bronobject** te configureren en uw kenmerktoewijzingen te testen met enkele testgebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en de gewenste resultaten opleveren, kunt u het filter verwijderen of het geleidelijk uitbreiden met meer gebruikers.

1. Stel op het tabblad **Inrichten** de **Inrichtingsstatus** in op **Aan**.

1. Selecteer **Bereik**. U kunt uit een van de volgende opties kiezen: 
   * **Alle gebruikers en groepen synchroniseren**: Selecteer deze optie als u van plan bent om de toegewezen kenmerken van alle gebruikers terug te schrijven van Azure AD naar SuccessFactors, afhankelijk van de bereikregels die zijn gedefinieerd onder **Toewijzingen** -> **Bereik van het bronobject**. 
   * **Alleen toegewezen gebruikers en groepen synchroniseren**: Selecteer deze optie als u van plan bent om toegewezen kenmerken terug te schrijven van alleen de gebruikers die u hebt toegewezen aan deze toepassing in de menu-optie **Toepassing** -> **Beheren** -> **Gebruikers en groepen**. Deze gebruikers zijn ook onderhevig aan de bereikregels die zijn gedefinieerd onder **Toewijzingen** -> **Bereik van bronobject**.

   > [!div class="mx-imgBorder"]
   > ![Bereik van Writeback selecteren](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > De inrichtings-app van SuccessFactors Writeback biedt geen ondersteuning voor ‘groepstoewijzing’. Alleen 'gebruikerstoewijzing' wordt ondersteund. 

1. Klik op **Opslaan**.

1. Met deze bewerking wordt de eerste synchronisatie gestart. Dit kan een variabel aantal uren duren, afhankelijk van het aantal gebruikers in de Azure AD-tenant en het bereik dat is gedefinieerd voor de bewerking. U kunt de voortgangsbalk controleren om de voortgang van de synchronisatiecyclus bij te houden. 

1. Controleer op elk gewenst moment het tabblad **Inrichtingslogboeken** in de Azure Portal om te zien welke acties de inrichtingsservice heeft uitgevoerd. In de inrichtingslogboeken worden alle afzonderlijke synchronisatiegebeurtenissen vermeld die worden uitgevoerd door de inrichtingsservice. 

1. Zodra de eerste synchronisatie is voltooid, wordt een rapport met een overzicht van de controle geschreven op het tabblad **Inrichten** geschreven, zoals hieronder wordt weergegeven.

   > [!div class="mx-imgBorder"]
   > ![Voortgangsbalk van het inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Ondersteunde scenario's, bekende problemen en beperkingen

Raadpleeg de [sectie Writeback-scenario's](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) van de handleiding voor integratie van SAP SuccessFactors. 

## <a name="next-steps"></a>Volgende stappen

* [Uitgebreide informatie over de integratie van Azure AD en SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen SuccessFactors en Azure Active Directory](successfactors-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtingsconfiguraties](../app-provisioning/export-import-provisioning-configuration.md)