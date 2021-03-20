---
title: Een gebruiker op aanvraag inrichten met behulp van Azure Active Directory
description: Synchronisatie afdwingen
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: be03a149f34c16621905081a2f9bb663d85bc53c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255658"
---
# <a name="on-demand-provisioning"></a>Inrichting on-demand
Inrichting op aanvraag gebruiken om binnen enkele seconden een gebruiker in te richten in een toepassing. U kunt met behulp van deze mogelijkheid onder andere het volgende:

* Configuratie problemen snel op te lossen.
* Expressies valideren die u hebt gedefinieerd.
* Test bereik filters.

## <a name="how-to-use-on-demand-provisioning"></a>Inrichting op aanvraag gebruiken

1. Meld u aan bij **Azure Portal**.
1. Ga naar **alle services**  >  **bedrijfs toepassingen**.
1. Selecteer uw toepassing en ga vervolgens naar de pagina inrichtings configuratie.
1. Configureer inrichting door uw beheerders referenties op te geven.
1. Selecteer **inrichten op aanvraag**.
1. Zoek naar een gebruiker op voor naam, achternaam, weergave naam, user principal name of e-mail adres.
   > [!NOTE]
   > Voor de Cloud HR-inrichtings toepassing (werkdag/SuccessFactors naar AD/Azure AD) is de invoer waarde anders. Geef voor het scenario voor werk dagen ' WID ' op voor de gebruiker in workday. Geef voor SuccessFactors scenario ' personIdExternal ' van de gebruiker op in SuccessFactors. 
 
1. Selecteer aan de onderkant van de pagina **inrichten** .

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Scherm opname van de Azure Portal gebruikers interface voor het inrichten van een gebruiker op aanvraag.":::

## <a name="understand-the-provisioning-steps"></a>De inrichtings stappen begrijpen

Het inrichtings proces op aanvraag probeert de stappen weer te geven die de inrichtings service onderneemt bij het inrichten van een gebruiker. Er zijn meestal vijf stappen voor het inrichten van een gebruiker. Een of meer van deze stappen, die in de volgende secties worden uitgelegd, worden weer gegeven tijdens de inrichtings ervaring op aanvraag.

### <a name="step-1-test-connection"></a>Stap 1: de verbinding testen

De inrichtings service probeert toegang te verlenen tot de doel toepassing door een aanvraag voor een test gebruiker te maken. De inrichtings service verwacht een antwoord dat aangeeft dat de service is gemachtigd om door te gaan met de inrichtings stappen. Deze stap wordt alleen weer gegeven wanneer het mislukt. Het wordt niet weer gegeven tijdens de inrichtings ervaring op aanvraag wanneer de stap is geslaagd.

#### <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

* Zorg ervoor dat u geldige referenties hebt ingevoerd, zoals het geheime token en de Tenant-URL, naar de doel toepassing. De vereiste referenties variëren per toepassing. Zie de [lijst met zelf](../saas-apps/tutorial-list.md)studies voor gedetailleerde configuratie handleidingen. 
* Zorg ervoor dat de doel toepassing filteren ondersteunt op de overeenkomende kenmerken die zijn gedefinieerd in het deel venster **kenmerk toewijzingen** . Mogelijk moet u de API-documentatie van de ontwikkelaar van de toepassing controleren om de ondersteunde filters te begrijpen.
* Voor systeem voor SCIM-toepassingen (Cross-Domain Identity Management) kunt u een hulp programma zoals postman gebruiken. Met deze hulpprogram ma's kunt u ervoor zorgen dat de toepassing reageert op autorisatie aanvragen op de manier waarop de inrichtings service van Azure Active Directory (Azure AD) verwacht. Bekijk een [voor beeld](./use-scim-to-provision-users-and-groups.md#request-3)van een aanvraag.

### <a name="step-2-import-user"></a>Stap 2: gebruiker importeren

Vervolgens haalt de inrichtings service de gebruiker op uit het bron systeem. De gebruikers kenmerken die door de service worden opgehaald, worden later gebruikt voor het volgende:

* Evalueren of de gebruiker binnen het bereik van de inrichting valt.
* Controleer het doel systeem voor een bestaande gebruiker.
* Bepaal welke gebruikers kenmerken er naar het doel systeem moeten worden geëxporteerd.

#### <a name="view-details"></a>Details weergeven


In het gedeelte **weergave Details** worden de eigenschappen van de gebruiker weer gegeven die zijn geïmporteerd uit het bron systeem (bijvoorbeeld Azure AD).

#### <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

* Het importeren van de gebruiker kan mislukken als het overeenkomende kenmerk ontbreekt voor het gebruikers object in het bron systeem. Probeer een van de volgende benaderingen om dit probleem op te lossen:

  * Werk het gebruikers object bij met een waarde voor het overeenkomende kenmerk.
  * Wijzig het overeenkomende kenmerk in uw inrichtings configuratie.

* Als een kenmerk dat u verwacht, ontbreekt in de geïmporteerde lijst, moet u ervoor zorgen dat het kenmerk een waarde heeft voor het gebruikers object in het bron systeem. De inrichtings service biedt momenteel geen ondersteuning voor het inrichten van Null-kenmerken.
* Zorg ervoor dat de pagina **kenmerk toewijzing** van uw inrichtings configuratie het kenmerk bevat dat u verwacht.

### <a name="step-3-determine-if-user-is-in-scope"></a>Stap 3: bepalen of de gebruiker binnen het bereik ligt

Vervolgens bepaalt de inrichtings service of de gebruiker binnen het [bereik](./how-provisioning-works.md#scoping) van de inrichting valt. De service beschouwt aspecten als:

* Hiermee wordt aangegeven of de gebruiker aan de toepassing is toegewezen.
* Hiermee wordt aangegeven of het bereik is ingesteld op **synchroniseren** of **Alles synchroniseren**.
* De bereik filters die in de inrichtings configuratie zijn gedefinieerd.  

#### <a name="view-details"></a>Details weergeven

De sectie **Details weer geven** toont de scope voorwaarden die zijn geëvalueerd. U ziet mogelijk een of meer van de volgende eigenschappen:

* **Actief in bron systeem** geeft aan dat de eigenschap is `IsActive` ingesteld op **waar** voor de gebruiker in azure AD.
* **Toegewezen aan toepassing** geeft aan dat de gebruiker is toegewezen aan de toepassing in azure AD.
* **Scope Sync All** geeft aan dat de bereik instelling alle gebruikers en groepen in de Tenant toestaat.
* **Gebruiker heeft vereiste rol** geeft aan dat de gebruiker de benodigde rollen heeft die moeten worden ingericht in de toepassing. 
* **Bereik filters** worden ook weer gegeven als u bereik filters hebt gedefinieerd voor uw toepassing. Het filter wordt weer gegeven met de volgende indeling: {filter titel voor bereik} {bereik filter kenmerk} {filter operator bereik} {filter waarde bereik}.

#### <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

* Zorg ervoor dat u een geldige bereik functie hebt gedefinieerd. Vermijd het gebruik van de [operator Greater_Than](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) met een niet-geheel getal.
* Als de gebruiker niet de vereiste rol heeft, raadpleegt [u de tips voor het inrichten van gebruikers die zijn toegewezen aan de standaard-Access-rol](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role).

### <a name="step-4-match-user-between-source-and-target"></a>Stap 4: identieke gebruikers tussen de bron en het doel

In deze stap probeert de service te voldoen aan de gebruiker die in de import stap is opgehaald met een gebruiker in het doel systeem.

#### <a name="view-details"></a>Details weergeven

Op de pagina **Details weer geven** ziet u de eigenschappen van de gebruikers die overeenkomen met het doel systeem. De eigenschappen die u in het context deel venster ziet, zijn als volgt afhankelijk:

* Als er geen gebruikers overeenkomen in het doel systeem, worden er geen eigenschappen weer geven.
* Als er sprake is van een gebruiker die overeenkomt met het doel systeem, ziet u de eigenschappen van die overeenkomende gebruiker van het doel systeem.
* Als er meerdere gebruikers overeenkomen, worden de eigenschappen van beide overeenkomende gebruikers weer geven.
* Als meerdere overeenkomende kenmerken deel uitmaken van uw kenmerk toewijzingen, wordt elk overeenkomend kenmerk opeenvolgend geëvalueerd en worden de overeenkomende gebruikers voor dat kenmerk weer gegeven.

#### <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

* De inrichtings service kan mogelijk niet overeenkomen met een gebruiker in het bron systeem die uniek is voor een gebruiker in het doel. Los dit probleem op door ervoor te zorgen dat het overeenkomende kenmerk uniek is.
* Zorg ervoor dat de doel toepassing filteren ondersteunt op het kenmerk dat is gedefinieerd als het overeenkomende kenmerk.  

### <a name="step-5-perform-action"></a>Stap 5: actie uitvoeren

Ten slotte neemt de inrichtings service een actie, zoals het maken, bijwerken, verwijderen of overs laan van de gebruiker.

Hier volgt een voor beeld van wat u kunt zien nadat het inrichten van een gebruiker op aanvraag is voltooid:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Scherm afbeelding met de geslaagde on-demand inrichting van een gebruiker.":::

#### <a name="view-details"></a>Details weergeven

In het gedeelte **weer gave Details** worden de kenmerken weer gegeven die zijn gewijzigd in de doel toepassing. Dit weer gave vertegenwoordigt de uiteindelijke uitvoer van de inrichtings service activiteit en de kenmerken die zijn geëxporteerd. Als deze stap mislukt, vertegenwoordigen de weer gegeven kenmerken de kenmerken die de inrichtings service heeft geprobeerd te wijzigen.

#### <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

* Fouten voor het exporteren van wijzigingen kunnen aanzienlijk verschillen. Raadpleeg de [documentatie voor het inrichtings logboek](../reports-monitoring/concept-provisioning-logs.md#error-codes) voor veelvoorkomende fouten.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

* **Moet u het inrichten uitschakelen voor het gebruik van on-demand inrichting?** Voor toepassingen die gebruikmaken van een Long-Bearer-token of een gebruikers naam en wacht woord voor autorisatie, zijn geen extra stappen vereist. Voor toepassingen waarvoor OAuth voor autorisatie wordt gebruikt, moet de inrichtings taak worden gestopt voordat het inrichten op aanvraag wordt gebruikt. Toepassingen zoals G suite, box, werk plek op Facebook en toegestane vertraging vallen in deze categorie. Er wordt gewerkt aan het ondersteunen van on-demand inrichten voor alle toepassingen, zonder dat het inrichten van taken hoeft te worden gestopt.

* **Hoe lang duurt het inrichten op aanvraag?** Inrichting op aanvraag duurt doorgaans minder dan 30 seconden.

## <a name="known-limitations"></a>Bekende beperkingen

Er zijn momenteel enkele bekende beperkingen voor het inrichten op aanvraag. Post uw [suggesties en feedback](https://aka.ms/appprovisioningfeaturerequest) zodat we beter kunnen bepalen welke verbeteringen er nu moeten worden gemaakt.

> [!NOTE]
> De volgende beperkingen zijn specifiek voor de inrichtings mogelijkheid op aanvraag. Raadpleeg de zelf studie voor de desbetreffende toepassing voor informatie over of een toepassing inrichtings groepen, verwijderingen of andere mogelijkheden ondersteunt.

* Amazon Web Services-toepassing (AWS) biedt geen ondersteuning voor inrichting op aanvraag. 
* Inrichting op aanvraag van groepen en rollen wordt niet ondersteund.
* Inrichting op aanvraag biedt ondersteuning voor het uitschakelen van gebruikers die de toewijzing van de toepassing ongedaan hebben gemaakt. Het is echter niet mogelijk om gebruikers uit te scha kelen of te verwijderen die zijn uitgeschakeld of verwijderd uit Azure AD. Deze gebruikers worden niet weer gegeven wanneer u zoekt naar een gebruiker.

## <a name="next-steps"></a>Volgende stappen

* [Probleem oplossing voor het inrichten](./application-provisioning-config-problem.md)