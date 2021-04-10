---
title: 'Zelfstudie: Binnenkomende inrichting voor Workday configureren in Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u binnenkomende inrichting van Workday naar Azure AD kunt configureren
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: ef4381f305292b366348aa3729209dc3f5e8c87b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954086"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Zelfstudie: Gebruikersinrichting configureren van Workday naar Azure AD
Het doel van deze zelfstudie is het weergeven van de stappen die u moet uitvoeren om werknemersgegevens van Workday in te richten in Azure Active Directory. 

>[!NOTE]
>Gebruik deze zelfstudie als de gebruikers die u wilt inrichten van Workday alleen cloudgebruikers zijn die geen on-premises AD-account nodig hebben. Als de gebruikers alleen een on-premises AD-account of zowel een AD-account als een Azure AD-account nodig hebben, raadpleegt u de tutorial [Workday configureren voor Active Directory](workday-inbound-tutorial.md) met meer informatie over het inrichten van gebruikers. 

## <a name="overview"></a>Overzicht

De [service voor het inrichten van gebruikers in Azure Active Directory](../app-provisioning/user-provisioning.md) integreert met de [Workday Human Resources-API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) om gebruikersaccounts in te richten. De werkstromen voor het inrichten van gebruikers in Workday die worden ondersteund door de Azure AD-service voor het inrichten van gebruikers, maken automatisering mogelijk van de volgende HR-scenario's en scenario's van beheer van identiteitslevenscycli:

* **Nieuwe medewerkers aannemen**: wanneer een nieuwe werknemer wordt toegevoegd aan Workday, wordt automatisch een gebruikersaccount gemaakt in Azure Active Directory en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund in Azure AD](../app-provisioning/user-provisioning.md), met een write-back van het e-mailadres naar Workday.

* **Updates van kenmerken en profielen van werknemers**: wanneer de record van een werknemer wordt bijgewerkt in Workday (zoals naam, functie of manager), wordt het gebruikersaccount automatisch bijgewerkt in Azure Active Directory en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund in Azure AD](../app-provisioning/user-provisioning.md).

* **Beëindiging van dienstverbanden**: wanneer een werknemer wordt verwijderd uit Workday, wordt het bijbehorende gebruikersaccount automatisch uitgeschakeld in Azure Active Directory en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund in Azure AD](../app-provisioning/user-provisioning.md).

* **Opnieuw aannemen van werknemers**: wanneer een werknemer opnieuw wordt toegevoegd in Workday, kan het oude gebruikersaccount automatisch opnieuw worden geactiveerd of ingericht (afhankelijk van wat uw voorkeur heeft) in Azure Active Directory en optioneel in Microsoft 365 en [andere SaaS-toepassingen die worden ondersteund in Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze oplossing voor het inrichten van gebruikers het meest geschikt?

Deze oplossing voor het inrichten van gebruikers van Workday naar Azure Active Directory is het meest geschikt voor:

* Organisaties die behoefte hebben aan een vooraf ontwikkelde, cloudoplossing voor het inrichten van gebruikers met Workday

* Organisaties die directe gebruikersinrichting van Workday naar Azure Active Directory vereisen

* Organisaties die vereisen dat gebruikers worden ingericht met behulp van gegevens die zijn verkregen door Workday

* Organisaties die Microsoft 365 als e-mailprogramma gebruiken

## <a name="solution-architecture"></a>Architectuur voor de oplossing

In deze sectie wordt de end-to-end-architectuur voor het inrichten van gebruikers voor cloudgebruikers beschreven. Er zijn twee gerelateerde stromen:

* **Gezaghebbende HR-gegevensstroom – van Workday naar Azure Active Directory:** In deze stroom worden werkgebeurtenissen (zoals nieuwe dienstverbanden, overplaatsingen en beëindiging van dienstverbanden) voor het eerst uitgevoerd in Workday, waarna de gebeurtenisgegevens naar Azure Active Directory stromen. Afhankelijk van de gebeurtenis kan dit in Azure AD leiden tot de bewerkingen maken, bijwerken, inschakelen en uitschakelen.
* **Stroom van terugschrijfbewerking – van on-premises Active Directory naar Workday:** Zodra het account is gemaakt in Active Directory, wordt het gesynchroniseerd met Azure AD via Azure AD Connect en kunnen gegevens zoals e-mailadres, gebruikersnaam en telefoonnummer worden teruggeschreven naar Workday.

  ![Overzicht](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Gegevensstroom van end-to-end-gebruikers

1. Het HR-team voert transacties voor werkrollen (nieuwe werknemers/overgeplaatste werknemers/werknemers die uit dienst treden, of nieuwe dienstverbanden/overplaatsingen/beëindiging van dienstverbanden) uit in Workday Employee Central
2. Met de Azure AD-inrichtingsservice worden geplande synchronisaties van identiteiten van Workday EC uitgevoerd. Daarnaast worden wijzigingen geïdentificeerd die moeten worden verwerkt om te synchroniseren met on-premises Active Directory.
3. De Azure AD-inrichtingsservice bepaalt de wijziging en roept de bewerking voor het maken/bijwerken/inschakelen/uitschakelen van de gebruiker aan in Azure AD.
4. Als de [Workday Writeback](workday-writeback-tutorial.md)-app is geconfigureerd, worden in Azure AD kenmerken zoals e-mailadres, gebruikersnaam en telefoonnummer opgehaald. 
5. De Azure AD-inrichtingsservice stelt e-mailadres, gebruikersnaam en telefoonnummer in Workday in.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Het configureren van gebruikersinrichting op basis van HR in de cloud vanuit Workday naar Azure AD vereist een degelijke planning waarbij rekening moet worden gehouden met verschillende aspecten, zoals:

* De overeenkomende id bepalen 
* Kenmerktoewijzing
* Kenmerktransformatie 
* Bereikfilters

Raadpleeg het [Implementatieplan voor Cloud HR](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richtlijnen rond deze onderwerpen. 

## <a name="configure-integration-system-user-in-workday"></a>De gebruiker van het integratiesysteem configureren in Workday

Raadpleeg de sectie [Gebruikers van een integratiesysteem configureren](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) voor het maken van een gebruikersaccount voor een Workday-integratiesysteem met machtigingen voor het ophalen van gegevens van werknemergegevens. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Gebruikersinrichting configureren van Workday naar Azure AD

In de volgende secties worden de stappen beschreven voor het configureren van gebruikersinrichting van Workday naar Azure AD voor cloudimplementaties.

* [De app voor de Azure AD-inrichtingsconnector toevoegen en de verbinding met Workday maken](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Workday- en Azure AD-kenmerktoewijzingen configureren](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Deel 1: De app voor de Azure AD-inrichtingsconnector toevoegen en de verbinding met Workday maken

**Ga als volgt te werk om de inrichting van Workday naar Azure Active Directory voor cloudgebruikers te configureren:**

1. Ga naar <https://portal.azure.com>.

2. Zoek en selecteer in de Azure-portal de optie **Azure Active Directory**.

3. Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen** en selecteer vervolgens de categorie **Alle**.

5. Zoek naar **Gebruikers inrichten van Workday naar Azure AD** en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weergegeven, selecteert u **Inrichten**.

7. Stel de **Inrichtings** **modus** in op **Automatisch**.

8. Ga als volgt te werk om de sectie **Referenties voor beheerders** af te ronden:

   * **Gebruikersnaam voor Workday**: Voer de gebruikersnaam van het account voor het Workday-integratiesysteem in, waarbij de domeinnaam van de tenant is toegevoegd. Het ziet er ongeveer als volgt uit: username@contoso4

   * **Wachtwoord voor Workday:** Voer het wachtwoord van het account voor het Workday-integratiesysteem in

   * **API-URL van Workday-webservices:** Geef de URL naar het eindpunt van Workday-webservices voor uw tenant op. De URL bepaalt de versie van de Workday-webservices-API die door de connector wordt gebruikt. 
   
     | URL-indeling | Gebruikte versie van WWS-API | XPATH-wijzigingen vereist |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | Nee |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | Nee |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v##.# | Ja |

      > [!NOTE]
     > Als er geen versiegegevens zijn opgegeven in de URL, gebruikt de app Workday-webservices (WWS) v21.1. Er zijn dan geen wijzigingen vereist voor de standaard API-expressies van XPATH die worden geleverd bij de app. Als u een specifieke API-versie van WWS wilt gebruiken, geeft u het versienummer op in de URL <br>
     > Voorbeeld: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Als u een WWS-API v30.0+ gebruikt voordat u de inrichtingstaak inschakelt, moet u de **API-expressies van XPATH** onder **Kenmerktoewijzing -> Geavanceerde opties-> Kenmerklijst bewerken voor Workday** verwijzen naar de sectie [Uw configuratie beheren](workday-inbound-tutorial.md#managing-your-configuration) en [Verwijzing naar Workday-kenmerk](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **E-mailmelding**: Voer uw e-mailadres in en zet een vinkje in het selectievakje 'E-mail verzenden als er een fout is opgetreden'.

   * Klik op de knop **Verbinding testen**.

   * Als de verbindingstest is gelukt, klikt u bovenaan op de knop **Opslaan**. Als de verbindingstest mislukt, controleert u of de Workday-URL en -referenties geldig zijn in Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Deel 2: Workday- en Azure AD-kenmerktoewijzingen configureren

In deze sectie configureert u hoe gebruikersgegevens stromen van Workday naar Azure Active Directory voor cloudgebruikers.

1. Ga naar het tabblad 'Inrichten' onder **Toewijzingen**. Klik vervolgens op **Werkrollen synchroniseren met Azure AD**.

2. In het veld **Bereik van bronobject** kunt u selecteren voor welke sets van gebruikers de inrichting naar Azure AD moet worden uitgevoerd. Dit doet u door een set van op kenmerken gebaseerde filters te definiëren. Het standaardbereik is 'alle gebruikers in Workday'. Voorbeelden van filters:

   * Voorbeeld: Bereik voor gebruikers met werknemer-id's tussen 1000000 en 2000000

      * Kenmerk: WorkerID

      * Operator: REGEX Match

      * Waarde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Voorbeeld: Alleen tijdelijke werknemers en geen reguliere werknemers

      * Kenmerk: ContingentID

      * Operator: IS NOT NULL

3. In het veld **Acties voor doelobject** kunt u globaal filteren op welke acties er worden uitgevoerd in Azure AD. **Maken** en **Bijwerken** worden het meest gebruikt.

4. In de sectie **Kenmerktoewijzingen** kunt u definiëren hoe afzonderlijke kenmerken van Workday worden toegewezen aan kenmerken van Active Directory.

5. Klik op een bestaande kenmerktoewijzing om deze bij te werken, of klik onderin het scherm op **Nieuwe toewijzing toevoegen** om nieuwe toewijzingen toe te voegen. Een afzonderlijke kenmerktoewijzing ondersteunt de volgende eigenschappen:

   * **Toewijzingstype**

      * **Direct**: de waarde van het Workday-kenmerk wordt weggeschreven naar het kenmerk van AD, zonder wijzigingen.

      * **Constante**: er wordt een waarde die bestaat uit een statische, constante tekenreeks weggeschreven naar het AD-kenmerk.

      * **Expressie**: hiermee kunt u een aangepaste waarde wegschrijven naar het AD-kenmerk, op basis van een of meer Workday-kenmerken. [Zie voor meer informatie dit artikel over expressies](../app-provisioning/functions-for-customizing-application-data.md).

   * **Bronkenmerk**: het gebruikerskenmerk uit Workday. Als het kenmerk dat u zoekt niet aanwezig is, raadpleegt u [De lijst met gebruikerskenmerken voor Workday aanpassen](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes).

   * **Standaardwaarde**: optioneel. Als het bronkenmerk een lege waarde heeft, wordt in plaats daarvan deze waarde weggeschreven door de toewijzing.
            De meest voorkomende configuratie is om dit leeg te laten.

   * **Doelkenmerk**: het gebruikerskenmerk in Azure AD.

   * **Objecten koppelen met dit kenmerk**: geeft aan of deze toewijzing moet worden gebruikt om gebruikers uniek te identificeren tussen Workday en Azure AD. Deze waarde wordt meestal ingesteld op het veld 'Werknemers-id voor Workday'. Dit veld wordt doorgaans toegewezen aan het (nieuwe) kenmerk 'Werknemers-id' of een extensiekenmerk in Azure AD.

   * **Prioriteit bij koppelen**: er kunnen meerdere overeenkomende kenmerken worden ingesteld. Wanneer er meerdere zijn, worden deze geëvalueerd in de volgorde die hier is gedefinieerd. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

   * **Deze toewijzing toepassen**

     * **Altijd**: deze toewijzing toepassen bij het maken en bijwerken van gebruikers.

     * **Alleen tijdens het maken van een object**: u kunt deze toewijzing alleen toepassen bij het maken van gebruikers

6. Als u uw toewijzingen wilt opslaan, klikt u op **Opslaan** bovenaan de sectie 'Kenmerktoewijzing'.


## <a name="enable-and-launch-user-provisioning"></a>Gebruikersinrichting inschakelen en starten

Zodra de configuratie van de Workday-inrichtings-app is voltooid, kunt u de inrichtingsservice inschakelen in Azure Portal.

> [!TIP]
> Wanneer u de inrichtingsservice inschakelt, worden er standaard inrichtingsbewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten zijn opgetreden in de toewijzing of er problemen zijn met Workday-gegevens, kan de inrichtingstaak mislukken en in de quarantaine-status gaan. Om dit te voorkomen, raden we u als best practice aan om het filter **Bereik van bronobject** te configureren en uw kenmerktoewijzingen te testen met enkele testgebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en de gewenste resultaten opleveren, kunt u het filter verwijderen of het geleidelijk uitbreiden met meer gebruikers.

1. Stel op het tabblad **Inrichten** de optie **Inrichtingsstatus** in op **Aan**.

2. Klik op **Opslaan**.

3. De eerste synchronisatie wordt nu gestart. Deze kan een wisselend aantal uren duren, afhankelijk van het aantal gebruikers in de Workday-tenant. U kunt de voortgang van de synchronisatiecyclus bijhouden door middel van de voortgangsbalk. 

4. Ga naar het tabblad **Auditlogboeken** in Azure Portal om te zien welke acties de inrichtingsservice heeft uitgevoerd. In de auditlogboeken worden alle afzonderlijke synchronisatiegebeurtenissen weergegeven die door de inrichtingsservice zijn uitgevoerd, bijvoorbeeld welke gebruikers zijn ingelezen vanuit Workday en vervolgens zijn toegevoegd aan of bijgewerkt in Azure Active Directory. 

5. Zodra de eerste synchronisatie is voltooid, wordt er een rapport met een overzicht van de controle weergegeven op het tabblad **Inrichten**, zoals u hieronder kunt zien.

   > [!div class="mx-imgBorder"]
   > ![Voortgangsbalk van het inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure AD-en workday-integratie scenario's en webservice-aanroepen](../app-provisioning/workday-integration-reference.md)
* [Informatie over ondersteunde Workday-kenmerken voor inkomende inrichting](../app-provisioning/workday-attribute-reference.md)
* [Informatie over het configureren van Workday-write-back](workday-writeback-tutorial.md)
* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Informatie over het configureren van eenmalige aanmelding tussen Workday en Azure Active Directory](workday-tutorial.md)
* [Meer informatie over het exporteren en importeren van uw inrichtingsconfiguraties](../app-provisioning/export-import-provisioning-configuration.md)


