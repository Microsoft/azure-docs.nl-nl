---
title: 'Zelfstudie: Workday write-back configureren in Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van kenmerk-write-back van Azure AD naar Workday
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: c65fddcc90b25f70759fb038a72dad0facfa99a9
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359728"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Zelfstudie: Kenmerk-write-back van Azure AD naar Workday configureren
Het doel van deze zelfstudie is om de stappen te laten zien die u moet uitvoeren voor write-back van kenmerken van Azure AD naar Workday. De inrichtings-app voor write-back van Workday ondersteunt het toewijzen van waarden aan de volgende centrale kenmerken van Workday:
* Werk-e-mailadres 
* Workday-gebruikersnaam
* Zakelijk vast telefoonnummer (inclusief landnummer, netnummer, nummer en toestelnummer)
* Primaire vlag zakelijk vast telefoonnummer
* Zakelijk mobiel nummer (inclusief landnummer, netnummer, nummer)
* Primaire vlag zakelijke mobiel

## <a name="overview"></a>Overzicht

Nadat u de integratie van binnenkomende inrichtingen hebt ingesteld met behulp van de inrichtingsapp [Workday naar on-premises AD](workday-inbound-tutorial.md) of de inrichtingsapp [Workday naar Azure AD](workday-inbound-cloud-only-tutorial.md), kunt u optioneel de app Workday Writeback configureren om contactgegevens, zoals zakelijk e-mailadres en telefoonnummer naar Workday te schrijven. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze oplossing voor het inrichten van gebruikers het meest geschikt?

Deze oplossing voor het inrichten van gebruikers van Workday Writeback is het meest geschikt voor:

* Organisaties die Microsoft 365 gebruiken om gezaghebbende kenmerken die door IT worden beheerd (zoals e-mailadres, gebruikersnaam en telefoonnummer), terug te schrijven naar Workday

## <a name="configure-integration-system-user-in-workday"></a>Integratiesysteemgebruiker configureren in Workday

Raadpleeg de sectie [Gebruikers van een integratiesysteem configureren](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) voor het maken van een gebruikersaccount voor een Workday-integratiesysteem met machtigingen voor het ophalen van gegevens van werknemergegevens. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Kenmerk-write-back van Azure AD naar Workday configureren

Volg deze instructies voor het configureren van write-back van gebruikers-e-mailadressen en gebruikersnaam van Azure Active Directory naar Workday.

* [De Writeback-connectorapp toevoegen en de verbinding met Workday maken](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Toewijzingen van write-back-kenmerken configureren](#part-2-configure-writeback-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Deel 1: De Writeback-connectorapp toevoegen en de verbinding met Workday maken

**De Workday Writeback-connector configureren:**

1. Ga naar <https://portal.azure.com>.

2. Zoek en selecteer in de Azure-portal de optie **Azure Active Directory**.

3. Selecteer **Ondernemingstoepassingen** en vervolgens **Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen** en selecteer vervolgens de categorie **Alle**.

5. Zoek naar **Workday Writeback** en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weergegeven, selecteert u **Inrichten**.

7. Stel de **Inrichtings** **modus** in op **Automatisch**.

8. Ga als volgt te werk om de sectie **Referenties voor beheerders** af te ronden:

   * **Gebruikersnaam van beheerder**: voer de gebruikersnaam van het account voor het Workday-integratiesysteem in, waarbij de domeinnaam van de tenant is toegevoegd. Het moet er ongeveer als volgt uitzien: *gebruikersnaam\@contoso4*

   * **Beheerderswachtwoord:** voer het wachtwoord van het account voor het Workday-integratiesysteem in

   * **Tenant-URL:** geef de URL naar het eindpunt van Workday-webservices voor uw tenant op. Deze waarde moet er als volgt uitzien: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources`, waarbij *contoso4* wordt vervangen door de juiste tenantnaam en *wd3-impl* wordt vervangen door de juiste omgevingstekenreeks (indien nodig).

   * **E-mailmelding**: voer uw e-mailadres in en zet een vinkje in het selectievakje 'E-mail verzenden als er een fout is opgetreden'.

   * Klik op de knop **Verbinding testen**. Als de verbindingstest is gelukt, klikt u bovenaan op de knop **Opslaan**. Als de verbindingstest mislukt, controleert u of de Workday-URL en -referenties geldig zijn in Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Deel 2: Toewijzingen van write-back-kenmerken configureren

In deze sectie configureert u hoe write-backkenmerken van Azure AD naar Workday stromen. 

1. Klik op het tabblad Inrichten onder **Toewijzingen** op de naam van de toewijzing.

2. In het veld **Bereik van bronobject** kunt u optioneel filteren welke gebruikerssets in Azure Active Directory deel moeten uitmaken van de write-back-functie. Het standaardbereik is ‘alle gebruikers in Azure AD’.

3. Werk in de sectie **Kenmerktoewijzingen** de overeenkomende id bij om het kenmerk in Azure Active Directory aan te geven waar de werkrol-id van Workday of de werknemers-id wordt opgeslagen. Een populaire overeenkomstmethode is het synchroniseren van de werkrol-id van Workday of de werknemers-id naar extensionAttribute1-15 in Azure AD. Vervolgens gebruikt u dit kenmerk in Azure AD om gebruikers terug te vinden in Workday.

4. Normaal gesproken wijst u het kenmerk *userPrincipalName* van Azure AD toe aan het Workday-kenmerk *UserID* en wijst u het kenmerk *mail* van Azure AD toe aan het Workday-kenmerk *EmailAddress*. 

     >[!div class="mx-imgBorder"]
     >![Azure-portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Gebruik de onderstaande richtlijnen voor het toewijzen van kenmerkwaarden voor telefoonnummers van Azure AD aan Workday. 

     | Workday-telefoonkenmerk | Verwachte waarde | Toewijzingsrichtlijnen |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | waar/onwaar | Constante of expressietoewijzing waarvan de uitvoer de tekenreekswaarde 'waar' of 'onwaar' is. |
     | WorkphoneLandlineCountryCodeName | [ISO 3166-1-landnummer van drie letters](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Constante of expressietoewijzing waarvan de uitvoer een landnummer van drie letters is. |
     | WorkphoneLandlineCountryCodeNumber | [Internationale landnummer voor bellen](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Constante of expressietoewijzing waarvan de uitvoer een geldig landnummer is (zonder het plusteken). |
     | WorkphoneLandlineNumber | Volledig telefoonnummer inclusief het netnummer | Wijs toe aan het kenmerk *telephoneNumber*. Gebruik regex om witruimte, vierkante haken en het landnummer te verwijderen. Zie onderstaand voorbeeld. |
     | WorkphoneLandlineExtension | Toestelnummer | Als *telephoneNumber* een toestelnummer bevat, gebruikt u regex om de waarde op te halen. |
     | WorkphoneMobileIsPrimary | waar/onwaar | Constante toewijzing of expressietoewijzing waarvan de uitvoer de tekenreekswaarde 'waar' of 'onwaar' is |
     | WorkphoneMobileCountryCodeName | [ISO 3166-1-landnummer van drie letters](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Constante of expressietoewijzing waarvan de uitvoer een landnummer van drie letters is. |
     | WorkphoneMobileCountryCodeNumber | [Internationale landnummer voor bellen](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Constante of expressietoewijzing waarvan de uitvoer een geldig landnummer is (zonder het plusteken). |
     | WorkphoneMobileNumber | Volledig telefoonnummer inclusief het netnummer | Wijs toe aan kenmerk *mobiel*. Gebruik regex om witruimte, vierkante haken en het landnummer te verwijderen. Zie onderstaand voorbeeld. |

     > [!NOTE]
     > Wanneer de Workday-webservice Change_Work_Contact wordt aangeroepen, verzendt Azure AD de volgende constante waarden: <br>
     > * **Communication_Usage_Type_ID** is ingesteld op de constante tekenreeks 'WERK' <br>
     > * **Phone_Device_Type_ID** is ingesteld op de constante tekenreeks 'Mobiel' voor mobiele telefoonnummers en op 'vast nummer' voor vaste telefoonnummers. <br>
     > 
     > U komt write-back-fouten tegen als uw Workday-tenant andere Type_IDs gebruikt. Als u dergelijke fouten wilt voorkomen, kunt u de Workday-taak **Referentie-id's behouden** gebruiken en de Type_IDs bijwerken zodat ze overeenkomen met de waarden die worden gebruikt door Azure AD. <br>
     >  

     **Referentie-regex-expressies - Voorbeeld 1**

     Gebruik de onderstaande reguliere expressie als het telefoonnummer in Azure AD is ingesteld met behulp van de vereiste indeling voor Self-service voor wachtwoordherstel (SSPR). <br>
     Voorbeeld: als de waarde voor het telefoonnummer +1 1112223333 is, -> voert de regex-expressie 1112223333 uit

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Referentie-regex-expressies - Voorbeeld 2**

     Gebruik de onderstaande reguliere expressie als het telefoonnummer in Azure AD is ingesteld met behulp van de indeling (XXX) XXX-XXXX. <br>
     Voorbeeld: als de waarde voor het telefoonnummer +111 222-3333 is, -> voert de regex-expressie 1112223333 uit

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Als u uw toewijzingen wilt opslaan, klikt u op **Opslaan** bovenaan de sectie 'Kenmerktoewijzing'.

## <a name="enable-and-launch-user-provisioning"></a>Gebruikersinrichting inschakelen en starten

Zodra de configuratie van de Workday-inrichtings-app is voltooid, kunt u de inrichtingsservice inschakelen in Azure Portal.

> [!TIP]
> Wanneer u de inrichtingsservice inschakelt, worden er standaard inrichtingsbewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten zijn opgetreden in de toewijzing of problemen zijn met Workday-gegevens, kan de inrichtingstaak mislukken en in de quarantaine-status gaan. Om dit te voorkomen, raden we u als best practice aan om het filter **Bereik van bronobject** te configureren en uw kenmerktoewijzingen te testen met enkele testgebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en de gewenste resultaten opleveren, kunt u het filter verwijderen of het geleidelijk uitbreiden met meer gebruikers.

1. Stel op het tabblad **Inrichten** de optie **Inrichtingsstatus** in op **Aan**.

1. Selecteer in de vervolgkeuzelijst **Bereik** de optie **Alle gebruikers en groepen synchroniseren**. Met deze schrijft de Writeback-app toegewezen kenmerken van alle gebruikers terug van Azure AD naar Workday, afhankelijk van de bereikregels die zijn gedefinieerd onder **Toewijzingen** -> **Bereik van het bronobject**. 

   > [!div class="mx-imgBorder"]
   > ![Bereik van write-back selecteren](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > De inrichtingsapp Workday Writeback biedt geen ondersteuning voor de optie **Alleen toegewezen gebruikers en groepen synchroniseren**.
 

2. Klik op **Opslaan**.

3. De eerste synchronisatie wordt nu gestart. Deze kan een wisselend aantal uren duren, afhankelijk van het aantal gebruikers in de bronmap. U kunt de voortgang van de synchronisatiecyclus bijhouden door middel van de voortgangsbalk. 

4. Controleer op elk gewenst moment het tabblad **Inrichtingslogboeken** in de Azure Portal om te zien welke acties de inrichtingsservice heeft uitgevoerd. In de auditlogboeken worden alle afzonderlijke synchronisatiegebeurtenissen vermeld die worden uitgevoerd door de inrichtingsservice, zoals welke gebruikers worden geïmporteerd uit de brontoepassing en worden geëxporteerd naar de doeltoepassing.  

5. Zodra de eerste synchronisatie is voltooid, wordt er een rapport met een overzicht weergegeven op het tabblad **Inrichten**, zoals u hieronder kunt zien.

     > [!div class="mx-imgBorder"]
     > ![Voortgangsbalk van het inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

* De Writeback-app maakt gebruik van een vooraf gedefinieerde waarde voor de parameters **Communication_Usage_Type_ID** en **Phone_Device_Type_ID**. Als uw Workday-tenant een andere waarde voor deze kenmerken gebruikt, mislukt de Writeback-bewerking. Een voorgestelde tijdelijke oplossing is om de Type_IDs in Workday bij te werken. 
* Wanneer de Writeback-app is geconfigureerd voor het bijwerken van secundaire telefoonnummers, wordt het bestaande secundaire telefoonnummer in Workday niet vervangen. Er wordt nog een secundair telefoon nummer aan de werkrolrecord toegevoegd. Er is geen tijdelijke oplossing voor dit gedrag. 


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Informatie over het configureren van eenmalige aanmelding tussen Workday en Azure Active Directory](workday-tutorial.md)
* [Informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtingsconfiguraties](../app-provisioning/export-import-provisioning-configuration.md)

