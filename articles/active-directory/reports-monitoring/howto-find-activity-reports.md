---
title: Rapporten van gebruikers activiteiten zoeken in Azure Portal | Microsoft Docs
description: Meer informatie over de Azure Active Directory activiteiten rapporten voor gebruikers in de Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42afa073da9197c12e4cbd316d311a7699d9a95f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013019"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Activiteitenrapporten zoeken in de Azure-portal

In dit artikel leert u hoe u rapporten van gebruikers activiteiten van Azure Active Directory (Azure AD) kunt vinden in de Azure Portal.

## <a name="audit-logs-report"></a>Rapport voor audittrails

In het rapport controle logboeken worden verschillende rapporten over toepassings activiteiten gecombineerd tot één weer gave voor rapportage op basis van context. Het rapport controle Logboeken openen:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
2. Selecteer de map in de rechter bovenhoek en selecteer vervolgens de Blade **Azure Active Directory** in het navigatie deel venster aan de linkerkant.
3. Selecteer **controle logboeken** in het gedeelte **activiteit** van de Blade Azure Active Directory. 

    ![Auditlogboeken](./media/howto-find-activity-reports/482.png "Auditlogboeken")

Het rapport controle logboeken consolideert de volgende rapporten:

* Auditrapport
* Activiteit voor wachtwoord opnieuw instellen
* Registratie activiteit voor wacht woord opnieuw instellen
* Activiteit van self-service groepen
* Wijzigingen in Office365-groeps naam
* Activiteit voor het inrichten van accounts
* Status van wacht woord rollover
* Fouten bij het inrichten van een account

### <a name="filtering-on-audit-logs"></a>Filteren op audit logboeken

U kunt Geavanceerd filteren in het controle rapport gebruiken om toegang te krijgen tot een specifieke categorie controle gegevens door deze op te geven in het **categorie** filter. Als u bijvoorbeeld alle activiteiten wilt weer geven die betrekking hebben op gebruikers, selecteert u de categorie **UserManagement** . 

Categorieën zijn onder andere:

- Alles
- AdministrativeUnit
- ApplicationManagement
- Verificatie
- Autorisatie
- Contactpersoon
- Apparaat
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Anders
- Beleid
- ResourceManagement
- RoleManagement
- UserManagement

U kunt ook filteren op een specifieke service met behulp van het vervolg filter voor de **service** . Als u bijvoorbeeld alle controle gebeurtenissen wilt ophalen die betrekking hebben op selfservice wachtwoord beheer, selecteert u het filter **selfservice wachtwoord beheer** .

Services omvatten:

- Alles
- Toegangsbeoordelingen
- Account inrichten 
- SSO van de toepassing
- Verificatiemethoden
- B2C
- Voorwaardelijke toegang
- Hoofddirectory
- Beheer rechten
- Identiteitsbeveiliging
- Uitgenodigde gebruikers
- PIM
- Self-service voor groepsbeheer
- Self-service voor wachtwoordbeheer
- Gebruiksvoorwaarden

## <a name="sign-ins-report"></a>Aanmeldingenrapport 

De weer gave **aanmeldingen** bevat alle gebruikers aanmeldingen en het gebruiks rapport van de **toepassing** . U kunt ook informatie over het gebruik van toepassingen weer geven in het gedeelte **beheren** van het overzicht **bedrijfs toepassingen** .

Het rapport aanmeldingen openen:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
2. Selecteer de map in de rechter bovenhoek en selecteer vervolgens de Blade **Azure Active Directory** in het navigatie deel venster aan de linkerkant.
3. Selecteer **aanmeldingen** in het gedeelte **activiteit** van de Blade Azure Active Directory. 

    ![Weer gave voor aanmeldingen](./media/howto-find-activity-reports/483.png "Weer gave voor aanmeldingen")


### <a name="filtering-on-application-name"></a>Filteren op toepassings naam

U kunt het rapport aanmeldingen gebruiken om details over toepassings gebruik te bekijken door te filteren op gebruikers naam of toepassings naam.

![Pagina Sign-In gebeurtenissen filteren](./media/howto-find-activity-reports/07.png "Pagina Sign-In gebeurtenissen filteren")

## <a name="security-reports"></a>Beveiligingsrapporten

### <a name="anomalous-activity-reports"></a>Rapporten over afwijkende activiteiten

Afwijkende activiteiten rapporten bieden informatie over beveiligings risico detecties die Azure AD kan detecteren en rapporteren.

De volgende tabel bevat de beveiligings rapporten van de afwijkende Azure AD-activiteiten en de bijbehorende typen risico detectie in de Azure Portal. Zie [Azure Active Directory-risico detectie](../identity-protection/overview-identity-protection.md)voor meer informatie.  


| Rapport over afwijkend Azure AD-activiteiten |  Type risico detectie identiteits bescherming|
| :--- | :--- |
| Gebruikers van wie de referenties zijn gelekt | Gelekte referenties |
| Onregelmatige aanmeldingsactiviteiten | Onmogelijke reis naar ongewone locaties |
| Aanmeldingen vanaf mogelijk geïnfecteerde apparaten | Aanmeldingen vanaf geïnfecteerde apparaten|
| Aanmeldingen van onbekende bronnen | Aanmeldingen vanaf anonieme IP-adressen |
| Aanmeldingen van IP-adressen met verdachte activiteit | Aanmeldingen van IP-adressen met verdachte activiteit |
| - | Aanmeldingen vanaf onbekende locaties |

De volgende afwijkende beveiligings rapporten van Azure AD worden niet opgenomen als risico detecties in de Azure Portal:

* Aanmeldingen na meerdere mislukte pogingen
* Aanmeldingen vanuit meerdere locaties


### <a name="detected-risk-detections"></a>Gedetecteerde risico detecties

U kunt rapporten over gedetecteerde risico detecties openen in het gedeelte **beveiliging** van de blade **Azure Active Directory** in de [Azure Portal](https://portal.azure.com). Gedetecteerde risico detecties worden bijgehouden in de volgende rapporten:   

- [Gebruikers die risico lopen](../identity-protection/overview-identity-protection.md)
- [Riskante aanmeldingen](../identity-protection/overview-identity-protection.md)

    ![Beveiligingsrapporten](./media/howto-find-activity-reports/04.png "Beveiligingsrapporten")

## <a name="troubleshoot-issues-with-activity-reports"></a>Problemen oplossen met activiteiten rapporten

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Ontbrekende gegevens in de gedownloade activiteiten logboeken

#### <a name="symptoms"></a>Symptomen 

Ik heb de activiteitenlogboeken (audit of aanmeldingen) gedownload en ik zie niet alle records voor de tijd die ik heb geselecteerd. Hoe komt dat? 

 ![Scherm afbeelding toont de knop downloaden in het activiteiten rapport.](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Oorzaak

Wanneer u activiteiten Logboeken in de Azure Portal downloadt, beperken we de schaal tot 250000 records, gesorteerd op meest recente eerst. 

#### <a name="resolution"></a>Oplossing

U kunt gebruikmaken van [API's van Azure AD Reporting](concept-reporting-api.md) om maximaal een miljoen records op te halen.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Ontbrekende controle gegevens voor recente acties in de Azure Portal

#### <a name="symptoms"></a>Symptomen

Ik heb enkele acties uitgevoerd in de Azure-portal en had verwacht de auditlogboeken voor deze acties te zien op de blade `Activity logs > Audit Logs`, maar ik kan ze niet vinden.

 ![Scherm afbeelding toont het activiteiten rapport.](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Oorzaak

Acties worden niet direct weergegeven in de activiteitenlogboeken. In de onderstaande tabel ziet u de latentiewaarden voor activiteitenlogboeken. 

| Rapport | Latentie (P95) | Latentie (P99) |
|--------|---------------|---------------|
| Directorycontrole | 2 minuten | 5 minuten |
| Aanmeldingsactiviteit | 2 minuten | 5 minuten |

#### <a name="resolution"></a>Oplossing

Wacht 15 minuten tot twee uur en kijk of de acties nu wel worden vermeld in het logboek. Als u de vermeldingen na twee uur nog steeds niet ziet, [maakt u een ondersteuningsticket aan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en dan gaan we aan de slag met het probleem.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Ontbrekende logboeken voor recente gebruikers aanmeldingen in het activiteiten logboek van Azure AD-aanmeldingen

#### <a name="symptoms"></a>Symptomen

Ik heb me onlangs aangemeld bij de Azure-portal en dan zou ik hier eigenlijk vermeldingen voor moeten zien in de logboeken op de blade `Activity logs > Sign-ins`, maar ik kan ze niet vinden.

 ![Scherm opname toont aanmeldingen voor Azure Active Directory.](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Oorzaak

Acties worden niet direct weergegeven in de activiteitenlogboeken. In de onderstaande tabel ziet u de latentiewaarden voor activiteitenlogboeken. 

| Rapport | Latentie (P95) | Latentie (P99) |
|--------|---------------|---------------|
| Directorycontrole | 2 minuten | 5 minuten |
| Aanmeldingsactiviteit | 2 minuten | 5 minuten |

#### <a name="resolution"></a>Oplossing

Wacht 15 minuten tot twee uur en kijk of de acties nu wel worden vermeld in het logboek. Als u de vermeldingen na twee uur nog steeds niet ziet, [maakt u een ondersteuningsticket aan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en dan gaan we aan de slag met het probleem.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Ik kan niet meer dan 30 dagen aan rapportgegevens bekijken in de Azure-portal

#### <a name="symptoms"></a>Symptomen

Ik kan niet meer dan 30 dagen aan aanmeldings- en controlegegevens bekijken in de Azure-portal. Hoe komt dat? 

 ![Scherm afbeelding toont het datum menu.](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Oorzaak

Afhankelijk van uw licentie bewaart Azure Active Directory Actions de activiteitenrapporten gedurende de volgende perioden:

| Rapport           | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---
| Directorycontrole  | 7 dagen        | 30 dagen             | 30 dagen             |
| Aanmeldingsactiviteit | Niet beschikbaar. U kunt gedurende zeven dagen uw eigen aanmeldingsactiviteit bekijken op de blade met uw individuele gebruikersprofiel. | 30 dagen | 30 dagen             |

Zie [Bewaarbeleid Azure Active Directory-rapporten](reference-reports-data-retention.md) voor meer informatie.  

#### <a name="resolution"></a>Oplossing

U hebt twee opties om de gegevens langer dan 30 dagen te bewaren. U kunt de [API's van de rapportagefunctie van Azure AD](concept-reporting-api.md) gebruiken om de gegevens via programmacode op te halen en op te slaan in een database. Een alternatief is om auditlogboeken te integreren in een SIEM-systeem van derden, zoals Splunk of SumoLogic.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van audit logboeken](concept-audit-logs.md)
* [Overzicht van aanmeldingen](concept-sign-ins.md)
* [Overzicht van Risk ante gebeurtenissen](../identity-protection/overview-identity-protection.md)