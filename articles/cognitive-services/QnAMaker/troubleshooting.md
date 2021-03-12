---
title: Problemen oplossen-QnA Maker
description: De lijst met alle meest gestelde vragen met betrekking tot de QnA Maker-service helpt u de service sneller en met betere resultaten te gebruiken.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 7b7ac20672ee653cbf6d2b82b7a9454c1d742b2c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612691"
---
# <a name="troubleshooting-for-qna-maker"></a>Problemen oplossen voor QnA Maker

De lijst met alle meest gestelde vragen met betrekking tot de QnA Maker-service helpt u de service sneller en met betere resultaten te gebruiken.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Voor spellingen beheren

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

<details>
<summary><b>Hoe kan ik de doorvoerprestaties voor queryvoorspellingen verbeteren?</b></summary>

**Antwoord**: problemen met doorvoer prestaties geven aan dat u de schaal van uw app service en uw Cognitive Search moet opschalen. Overweeg een replica toe te voegen aan uw Cognitive Search om de prestaties te verbeteren.

Meer informatie over [prijs categorieën](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Het QnAMaker-service-eind punt ophalen</b></summary>

**Antwoord**: QnAMaker service-eind punt is handig voor fout opsporing wanneer u contact opneemt met QnAMaker-ondersteuning of UserVoice. Het eind punt is een URL in dit formulier: `https://your-resource-name.azurewebsites.net` .

1. Ga naar de QnAMaker-service (resource groep) in het [Azure Portal](https://portal.azure.com)

    ![QnAMaker Azure-resource groep in Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecteer de App Service die aan de QnA Maker resource is gekoppeld. Normaal gesp roken zijn de namen hetzelfde.

     ![QnAMaker App Service selecteren](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. De eind punt-URL is beschikbaar in het gedeelte Overzicht

    ![QnAMaker-eind punt](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

<details>
<summary><b>Hoe kan ik de doorvoerprestaties voor queryvoorspellingen verbeteren?</b></summary>

**Antwoord**: problemen met doorvoer prestaties geven aan dat u uw Cognitive Search moet opschalen. Overweeg een replica toe te voegen aan uw Cognitive Search om de prestaties te verbeteren.

Meer informatie over [prijs categorieën](Concepts/azure-resources.md).
</details>

---

## <a name="manage-the-knowledge-base"></a>De Knowledge Base beheren

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

<details>
<summary><b>Ik heb per ongeluk een deel van mijn QnA Maker verwijderd. Wat moet ik doen?</b></summary>

**Antwoord**: Verwijder geen van de Azure-Services die zijn gemaakt samen met de QnA Maker resource, zoals een zoek-of web-app. Deze zijn nodig om QnA Maker te kunnen werken, als u er een verwijdert, QnA Maker niet meer goed werkt.

Alle verwijderingen zijn permanent, inclusief vraag-en antwoord paren, bestanden, Url's, aangepaste vragen en antwoorden, kennis slagen of Azure-resources. Zorg ervoor dat u de Knowledge Base exporteert vanaf de pagina **instellingen** voordat u een deel van de Knowledge Base verwijdert.

</details>

<details>
<summary><b>Waarom worden mijn URL ('s)/file (en) niet de vraag/antwoord paren uitgepakt?</b></summary>

**Antwoord**: het is mogelijk dat QnA Maker een bepaalde vraag-en antwoord-inhoud (QnA) niet automatisch kan ophalen uit geldige url's voor veelgestelde vragen. In dergelijke gevallen kunt u de QnA-inhoud in een txt-bestand plakken en zien of het hulp programma deze kan opnemen. U kunt ook inhoud toevoegen aan uw Knowledge Base via de [QnA Maker Portal](https://qnamaker.ai).

</details>

<details>
<summary><b>Hoe groot kan ik een Knowledge Base maken?</b></summary>

**Antwoord**: de grootte van de kennis basis is afhankelijk van de SKU van Azure Search die u kiest bij het maken van de QnA Maker service. Lees [hier](./concepts/azure-resources.md) meer informatie.

</details>

<details>
<summary><b>Waarom kan er niets in de vervolg keuzelijst worden weer geven wanneer ik een nieuwe Knowledge Base probeer te maken?</b></summary>

**Antwoord**: u hebt nog geen QnA Maker Services in azure gemaakt. Lees [hier](./How-To/set-up-qnamaker-service-azure.md) meer informatie over hoe u dit doet.

</details>

<details>
<summary><b>Hoe kan ik een kennis database delen met anderen?</b></summary>

**Antwoord**: delen werkt op het niveau van een QnA Maker-service, dat wil zeggen dat alle kennis grondslagen in de service worden gedeeld. Lees [hier](./index.yml) hoe u kunt samen werken aan een Knowledge Base.

</details>

<details>
<summary><b>Kunt u een Knowledge Base delen met een Inzender die zich niet in dezelfde AAD-Tenant bevindt, om een Knowledge Base te wijzigen?</b></summary>

**Antwoord**: delen is gebaseerd op op rollen gebaseerd toegangs beheer van Azure (Azure RBAC). Als u _een_ resource in azure kunt delen met een andere gebruiker, kunt u ook QnA Maker delen.

</details>

<details>
<summary><b>Als u een abonnement hebt App Service met 5 QnAMaker Knowledge bases. Kunt u lees-en schrijf rechten toewijzen aan vijf verschillende gebruikers, zodat ze slechts één QnAMaker Knowledge Base kunnen openen?</b></summary>

**Antwoord**: u kunt een volledige QnAMaker-service delen, niet individuele kennis bases.

</details>

<details>
<summary><b>Hoe kan ik het standaard bericht wijzigen wanneer er geen goede overeenkomst wordt gevonden?</b></summary>

**Antwoord**: het standaard bericht maakt deel uit van de instellingen in de app service.
- Ga naar de app service-resource in de Azure Portal

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klik op de optie **instellingen**

![qnamaker appservice-instellingen](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Wijzig de waarde van de instelling **DefaultAnswer**
- Uw app service opnieuw starten

![qnamaker appservice opnieuw opstarten](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Waarom wordt mijn share point-koppeling niet opgehaald?</b></summary>

**Antwoord**: Zie [gegevens bron locaties](./concepts/data-sources-and-content.md#data-source-locations) voor meer informatie.

</details>

<details>
<summary><b>De updates die ik heb aangebracht in mijn Knowledge Base, worden niet weer gegeven bij het publiceren. Waarom niet?</b></summary>

**Antwoord**: elke bewerking die in een tabel wordt bijgewerkt, getest of ingesteld, moet worden opgeslagen voordat deze kan worden gepubliceerd. Zorg ervoor dat u na elke bewerking op de knop **opslaan en trainen** klikt.

</details>

<details>
<summary><b>Ondersteunt de Knowledge Base uitgebreide gegevens of multi media?</b></summary>

**Antwoord**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatisch uitpakken van multi media voor bestanden en Url's

* URL'S-beperkte conversie van HTML naar prijs verlaging.
* Bestanden-niet ondersteund

#### <a name="answer-text-in-markdown"></a>Antwoord tekst in de prijs
Zodra de QnA-paren zich in de Knowledge Base bevinden, kunt u de geprijsde tekst van een antwoord bewerken om koppelingen op te geven naar de media die beschikbaar zijn via open bare Url's.


</details>

<details>
<summary><b>Ondersteunt QnA Maker niet-Engelse talen?</b></summary>

**Antwoord**: Zie meer details over [ondersteunde talen](./overview/language-support.md).

Als u inhoud uit meerdere talen hebt, moet u ervoor zorgen dat u voor elke taal een afzonderlijke service maakt.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

<details>
<summary><b>Waarom worden mijn URL ('s)/file (en) niet de vraag/antwoord paren uitgepakt?</b></summary>

**Antwoord**: het is mogelijk dat QnA Maker een bepaalde vraag-en antwoord-inhoud (QnA) niet automatisch kan ophalen uit geldige url's voor veelgestelde vragen. In dergelijke gevallen kunt u de QnA-inhoud in een txt-bestand plakken en zien of het hulp programma deze kan opnemen. U kunt ook inhoud toevoegen aan uw Knowledge Base via de [QnA Maker Portal](https://qnamaker.ai).

</details>

<details>
<summary><b>Hoe groot kan ik een Knowledge Base maken?</b></summary>

**Antwoord**: de grootte van de kennis basis is afhankelijk van de SKU van Azure Search die u kiest bij het maken van de QnA Maker service. Lees [hier](./concepts/azure-resources.md) meer informatie.

</details>

<details>
<summary><b>Waarom kan er niets in de vervolg keuzelijst worden weer geven wanneer ik een nieuwe Knowledge Base probeer te maken?</b></summary>

**Antwoord**: u hebt nog geen QnA Maker Services in azure gemaakt. Lees [hier](./How-To/set-up-qnamaker-service-azure.md) meer informatie over hoe u dit doet.

</details>

<details>
<summary><b>Hoe kan ik een kennis database delen met anderen?</b></summary>

**Antwoord**: delen werkt op het niveau van een QnA Maker-service, dat wil zeggen dat alle kennis grondslagen in de service worden gedeeld. Lees [hier](./index.yml) hoe u kunt samen werken aan een Knowledge Base.

</details>

<details>
<summary><b>Kunt u een Knowledge Base delen met een Inzender die zich niet in dezelfde Azure Active Directory Tenant bevindt, om een Knowledge Base te wijzigen?</b></summary>

**Antwoord**: delen is gebaseerd op op rollen gebaseerd toegangs beheer van Azure (Azure RBAC). Als u _een_ resource in azure kunt delen met een andere gebruiker, kunt u ook QnA Maker delen.

</details>

<details>
<summary><b>Kunt u lees-en schrijf rechten toewijzen aan vijf verschillende gebruikers, zodat ze slechts één QnAMaker Knowledge Base kunnen openen?</b></summary>

**Antwoord**: u kunt een volledige QnAMaker-service delen, niet individuele kennis bases.

</details>

<details>
<summary><b>Waarom wordt mijn share point-koppeling niet opgehaald?</b></summary>

**Antwoord**: Zie [gegevens bron locaties](./concepts/data-sources-and-content.md#data-source-locations) voor meer informatie.

</details>

<details>
<summary><b>De updates die ik heb aangebracht in mijn Knowledge Base, worden niet weer gegeven bij het publiceren. Waarom niet?</b></summary>

**Antwoord**: elke bewerking die in een tabel wordt bijgewerkt, getest of ingesteld, moet worden opgeslagen voordat deze kan worden gepubliceerd. Zorg ervoor dat u na elke bewerking op de knop **opslaan en trainen** klikt.

</details>

<details>
<summary><b>Ondersteunt de Knowledge Base uitgebreide gegevens of multi media?</b></summary>

**Antwoord**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatisch uitpakken van multi media voor bestanden en Url's

* URL'S-beperkte conversie van HTML naar prijs verlaging.
* Bestanden-niet ondersteund

#### <a name="answer-text-in-markdown"></a>Antwoord tekst in de prijs
Zodra de QnA-paren zich in de Knowledge Base bevinden, kunt u de geprijsde tekst van een antwoord bewerken om koppelingen op te geven naar de media die beschikbaar zijn via open bare Url's.


</details>

<details>
<summary><b>Ondersteunt QnA Maker niet-Engelse talen?</b></summary>

**Antwoord**: Zie meer details over [ondersteunde talen](./overview/language-support.md).

Als u inhoud uit meerdere talen hebt, moet u ervoor zorgen dat u voor elke taal een afzonderlijke service maakt.

</details>

---

## <a name="manage-service"></a>Services beheren

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

<details>
<summary><b>Wanneer moet ik mijn app service opnieuw starten?</b></summary>

**Antwoord**: Vernieuw uw app service wanneer het waarschuwings pictogram wordt naast de versie waarde voor de Knowledge Base in de tabel met **eindpunt sleutels** op de [pagina](https://www.qnamaker.ai/UserSettings) **gebruikers instellingen** .

</details>

<details>
<summary><b>Ik heb mijn bestaande zoek service verwijderd. Hoe kan ik dit oplossen?</b></summary>

**Antwoord**: als u een Azure Cognitive search-index verwijdert, is de bewerking definitief en kan de index niet worden hersteld.

</details>

<details>
<summary><b>Ik heb mijn `testkb` index in mijn zoek service verwijderd. Hoe kan ik dit oplossen?</b></summary>

**Antwoord**: uw oude gegevens kunnen niet worden hersteld. Maak een nieuwe QnA Maker resource en maak uw Knowledge Base opnieuw.

</details>

<details>
<summary><b>Wanneer moet ik mijn eindpunt sleutels vernieuwen?</b></summary>

**Antwoord**: Vernieuw uw eindpunt sleutels als u vermoedt dat ze zijn aangetast.

</details>

<details>
<summary><b>Kan ik dezelfde Azure Cognitive Search Resource gebruiken voor Knowledge bases met behulp van meerdere talen?</b></summary>

**Antwoord**: als u meerdere talen en meerdere kennis slagen wilt gebruiken, moet de gebruiker voor elke taal een QnA Maker resource maken. Hiermee maakt u een afzonderlijke Azure Search-service per taal. Het mixen van verschillende taal kennis bases in één Azure Search-service resulteert in een gedegradeerde relevantie van de resultaten.

</details>

<details>
<summary><b>Hoe kan ik de naam wijzigen van de Azure Cognitive Search-resource die wordt gebruikt door QnA Maker?</b></summary>

**Antwoord**: de naam van de Azure-Cognitive Search resource is de naam van de QnA Maker resource met enkele wille keurige letters die aan het einde worden toegevoegd. Dit maakt het moeilijk om onderscheid te maken tussen meerdere zoek bronnen voor QnA Maker. Maak een afzonderlijke zoek service (noem deze zoals u dat wilt) en verbind deze met uw QnA-service. De stappen zijn vergelijkbaar met de stappen die u moet uitvoeren om [een upgrade van Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)uit te voeren.

</details>

<details>
<summary><b>Als QnA Maker retourneert `Runtime core is not initialized,` Hoe kan ik het probleem oplossen?</b></summary>

**Antwoord**: de schijf ruimte voor uw app-service is mogelijk vol. Stappen om uw schijf ruimte te herstellen:

1. Selecteer in de [Azure Portal](https://portal.azure.com)de app service van uw QnA maker en Stop vervolgens de service.
1. Terwijl u nog steeds op de app-service klikt, selecteert u **ontwikkelingsprogram ma's** en **Geavanceerde hulpprogram Ma's**. vervolgens **gaat u naar**. Hiermee opent u een nieuw browser venster.
1. Selecteer **console fout opsporing** en vervolgens **cmd** om een opdracht regel programma te openen.
1. Ga naar de _site/wwwroot/data/QnAMaker/_ map.
1. Verwijder alle mappen waarvan de naam begint met `rd` .

    **Verwijder niet** het volgende:

    * KbIdToRankerMappings.txt-bestand
    * EndpointSettings.jsvoor bestand
    * Map EndpointKeys

1. Start de app service.
1. Open uw kennis database om te controleren of deze nu werkt.

</details>
<details>
<summary><b>Waarom werkt mijn Application Insights niet?</b></summary>

**Antwoord**: u kunt de onderstaande stappen door nemen en bij te werken om het probleem op te lossen:

1. In App Service-> instellingen groep-> configuratie sectie-> toepassings instellingen-> naam para meters ' UserAppInsightsKey ' is correct geconfigureerd en ingesteld op het bijbehorende tabblad Overzicht van Application Insights ("instrumentatie sleutel") GUID. 

1. In App Service-> instellingen groep-> Application Insights sectie-> moet u ervoor zorgen dat app Insights is ingeschakeld en is verbonden met de betreffende Application Insights-resource.

</details>

<details>
<summary><b>Mijn Application Insights is ingeschakeld, maar waarom werkt het niet goed?</b></summary>

**Antwoord**: Voer de onderstaande stappen uit: 

1.  Kopieer de waarde ' "APPINSIGHTS_INSTRUMENTATIONKEY" name ' naar de naam van de UserAppInsightsKey door te overschrijven als er al een waarde aanwezig is. 

1.  Als de sleutel ' UserAppInsightsKey ' niet voor komt in de app-instellingen, voegt u een nieuwe sleutel toe met die naam en kopieert u de waarde.

1.  Sla het op en Hiermee wordt de app service automatisch opnieuw gestart. Hiermee wordt het probleem opgelost. 

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)


<details>
<summary><b>Ik heb mijn bestaande zoek service verwijderd. Hoe kan ik dit oplossen?</b></summary>

**Antwoord**: als u een Azure Cognitive search-index verwijdert, is de bewerking definitief en kan de index niet worden hersteld.

</details>

<details>
<summary><b>Ik heb mijn `testkb` index in mijn zoek service verwijderd. Hoe kan ik dit oplossen?</b></summary>

**Antwoord**: uw oude gegevens kunnen niet worden hersteld. Maak een nieuwe QnA Maker resource en maak uw Knowledge Base opnieuw.

</details>

<details>
<summary><b>Kan ik dezelfde Azure Cognitive Search Resource gebruiken voor Knowledge bases met behulp van meerdere talen?</b></summary>

**Antwoord**: als u meerdere talen en meerdere kennis slagen wilt gebruiken, moet de gebruiker voor elke taal een QnA Maker resource maken. Hiermee maakt u een afzonderlijke Azure Search-service per taal. Het mixen van verschillende taal kennis bases in één Azure Search-service resulteert in een gedegradeerde relevantie van de resultaten.

</details>

<details>
<summary><b>Hoe kan ik de naam wijzigen van de Azure Cognitive Search-resource die wordt gebruikt door QnA Maker?</b></summary>

**Antwoord**: de naam van de Azure-Cognitive Search resource is de naam van de QnA Maker resource met enkele wille keurige letters die aan het einde worden toegevoegd. Dit maakt het moeilijk om onderscheid te maken tussen meerdere zoek bronnen voor QnA Maker. Maak een afzonderlijke zoek service (noem deze zoals u dat wilt) en verbind deze met uw QnA-service. De stappen zijn vergelijkbaar met de stappen die u moet uitvoeren om [een upgrade van Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)uit te voeren.

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integreren met andere services, met inbegrip van bots

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

<details>
<summary><b>Moet ik bot Framework gebruiken om QnA Maker te kunnen gebruiken?</b></summary>

**Antwoord**: Nee, u hoeft niet het [bot-Framework](https://github.com/Microsoft/botbuilder-dotnet) met QnA Maker te gebruiken. QnA Maker wordt echter aangeboden als een van de verschillende sjablonen in [Azure bot service](/azure/bot-service/). Met de bot-service kunt u snel slimme bot-ontwikkeling maken via micro soft bot Framework en wordt uitgevoerd in een omgeving zonder servers.

</details>

<details>
<summary><b>Hoe kan ik een nieuwe bot maken met QnA Maker?</b></summary>

**Antwoord**: Volg de instructies in [deze](./Quickstarts/create-publish-knowledge-base.md) documentatie om uw Bot te maken met Azure bot service.

</details>

<details>
<summary><b>Hoe kan ik een andere Knowledge Base gebruiken met een bestaande Azure bot-service?</b></summary>

**Antwoord**: u moet de volgende informatie over uw Knowledge Base hebben:

* De Knowledge Base-ID.
* De geplaatste aangepaste subdomeinnaam van het gepubliceerde eind punt, dat wil zeggen `host` , op de pagina **instellingen** wordt weer gegeven nadat u deze hebt gepubliceerd.
* De gepubliceerde eindpunt sleutel van de Knowledge Base-gevonden op de pagina **instellingen** nadat u deze hebt gepubliceerd.

Met deze informatie gaat u naar de app-service van uw bot in de Azure Portal. Wijzig de waarden onder **instellingen-> configuratie-> toepassings instellingen**.

De eindpunt sleutel van de Knowledge Base bevindt zich `QnAAuthkey` in de ABS-service.

</details>

<details>
<summary><b>Kunnen twee of meer client toepassingen een Knowledge Base delen?</b></summary>

**Antwoord**: Ja, de Knowledge Base kan vanuit elk wille keurig aantal clients worden opgevraagd. Als de reactie van de Knowledge Base langzaam of time-out lijkt, kunt u overwegen om de servicelaag bij te werken voor de app service die is gekoppeld aan de Knowledge Base.

</details>

<details>
<summary><b>Hoe kan ik de QnA Maker-service insluiten in mijn website?</b></summary>

**Antwoord**: Volg deze stappen om de QnA Maker-service in te sluiten als een web-chat besturings element in uw website:

1. Maak uw FAQ-bot door de volgende [instructies te volgen.](./Quickstarts/create-publish-knowledge-base.md)
2. Schakel de volgende stappen uit om Web Chat [in te scha](/azure/bot-service/bot-service-channel-connect-webchat) kelen

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)


<details>
<summary><b>Moet ik bot Framework gebruiken om QnA Maker te kunnen gebruiken?</b></summary>

**Antwoord**: Nee, u hoeft niet het [bot-Framework](https://github.com/Microsoft/botbuilder-dotnet) met QnA Maker te gebruiken. QnA Maker wordt echter aangeboden als een van de verschillende sjablonen in [Azure bot service](/azure/bot-service/). Met de bot-service kunt u snel slimme bot-ontwikkeling maken via micro soft bot Framework en wordt uitgevoerd in een omgeving zonder servers.

</details>

<details>
<summary><b>Hoe kan ik een nieuwe bot maken met QnA Maker?</b></summary>

**Antwoord**: Volg de instructies in [deze](./Quickstarts/create-publish-knowledge-base.md) documentatie om uw Bot te maken met Azure bot service.

</details>

<details>
<summary><b>Hoe kan ik een andere Knowledge Base gebruiken met een bestaande Azure bot-service?</b></summary>

**Antwoord**: u moet de volgende informatie over uw Knowledge Base hebben:

* De Knowledge Base-ID.
* De geplaatste aangepaste subdomeinnaam van het gepubliceerde eind punt, dat wil zeggen `host` , op de pagina **instellingen** wordt weer gegeven nadat u deze hebt gepubliceerd.
* De gepubliceerde eindpunt sleutel van de Knowledge Base-gevonden op de pagina **instellingen** nadat u deze hebt gepubliceerd.

Met deze informatie gaat u naar de app-service van uw bot in de Azure Portal. Wijzig de waarden onder **instellingen-> configuratie-> toepassings instellingen**.

De eindpunt sleutel van de Knowledge Base bevindt zich `QnAAuthkey` in de ABS-service.

</details>

<details>
<summary><b>Kunnen twee of meer client toepassingen een Knowledge Base delen?</b></summary>

**Antwoord**: Ja, de Knowledge Base kan vanuit elk wille keurig aantal clients worden opgevraagd. Als de reactie van de Knowledge Base langzaam of time-out lijkt, kunt u overwegen om de servicelaag bij te werken voor de app service die is gekoppeld aan de Knowledge Base.

</details>

<details>
<summary><b>Hoe kan ik de QnA Maker-service insluiten in mijn website?</b></summary>

**Antwoord**: Volg deze stappen om de QnA Maker-service in te sluiten als een web-chat besturings element in uw website:

1. Maak uw FAQ-bot door de volgende [instructies te volgen.](./Quickstarts/create-publish-knowledge-base.md)
2. Schakel de volgende stappen uit om Web Chat [in te scha](/azure/bot-service/bot-service-channel-connect-webchat) kelen

---

## <a name="data-storage"></a>Gegevensopslag

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

<details>
<summary><b>Welke gegevens worden opgeslagen en waar wordt deze opgeslagen?</b></summary>

**Antwoord**:

Wanneer u uw QnA Maker-service maakt, hebt u een Azure-regio geselecteerd. Uw kennis-en logboek bestanden worden opgeslagen in deze regio.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

<details>
<summary><b>Welke gegevens worden opgeslagen en waar wordt deze opgeslagen?</b></summary>

**Antwoord**:

Wanneer u uw QnA Maker-service maakt, hebt u een Azure-regio geselecteerd. Uw kennis-en logboek bestanden worden opgeslagen in deze regio.

</details>

---
