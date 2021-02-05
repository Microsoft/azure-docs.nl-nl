---
title: Beveiligingsscore in Azure Security Center
description: Beschrijving van de beveiligde Score van Azure Security Center en de bijbehorende beveiligings controles
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: bd64ca877f0bfd81597e091ef8ef000ad1b76992
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99526429"
---
# <a name="secure-score-in-azure-security-center"></a>Beveiligingsscore in Azure Security Center

## <a name="introduction-to-secure-score"></a>Inleiding tot beveiligde Score

Azure Security Center heeft twee hoofd doelen: 

- om inzicht te krijgen in uw huidige beveiligings situatie
- om u te helpen uw beveiliging efficiënt en effectief te verbeteren

De centrale functie in Security Center waarmee u deze doelen kunt bereiken, is een **veilige Score**.

Security Center controleert uw resources, abonnementen en organisatie doorlopend op beveiligingsproblemen. Vervolgens worden alle bevindingen tot één enkele score samengevoegd, zodat u in een oogopslag uw huidige beveiligingssituatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risiconiveau is.

De beveiligde score wordt weer gegeven op de Azure Portal pagina's als een percentage waarde, maar de onderliggende waarden worden ook duidelijk weer gegeven:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Algemene beveiligde Score zoals weer gegeven in de portal":::

Bekijk de pagina met aanbevelingen van Security Center voor de uitstaande acties die nodig zijn om uw score te verhogen om uw beveiliging te verbeteren. Elke aanbeveling bevat instructies om u te helpen bij het oplossen van het specifieke probleem.

Aanbevelingen zijn onderverdeeld in **beveiligings controles**. Elk besturings element is een logische groep gerelateerde beveiligings aanbevelingen en weerspiegelt uw kwets bare aanvals oppervlakken. Uw score is alleen verbeterd wanneer u *alle* aanbevelingen voor één resource in een besturings element herstelt. Als u wilt zien hoe goed uw organisatie elke afzonderlijke kwets baarheid beveiligt, controleert u de scores voor elk beveiligings beheer.

Zie [hoe uw beveiligde score wordt berekend](secure-score-security-controls.md#how-your-secure-score-is-calculated) hieronder voor meer informatie. 


## <a name="access-your-secure-score"></a>Toegang tot uw beveiligde Score

U kunt uw algemene beveiligde Score vinden, evenals uw score per abonnement, via de Azure Portal of via het programma, zoals beschreven in de volgende secties:

- [Uw beveiligde Score ophalen uit de portal](#get-your-secure-score-from-the-portal)
- [Uw beveiligde Score ophalen uit de REST API](#get-your-secure-score-from-the-rest-api)
- [Uw beveiligde Score ophalen uit de Azure-resource grafiek (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Uw beveiligde Score ophalen uit de portal

Security Center wordt uw score prominent weer gegeven in de portal: het is de eerste hoofd tegel van de Security Center overzichts pagina. Als u deze tegel selecteert, gaat u naar de pagina speciale beveiligde Score, waar u de score kunt zien die is opgesplitst per abonnement. Selecteer één abonnement voor een overzicht van de gedetailleerde lijst met aanbevelingen met prioriteit en de mogelijke gevolgen voor het oplossen van deze voor de Score van het abonnement. 

Voor samen vatting wordt uw beveiligde Score op de volgende locaties weer gegeven in de portal pagina's van Security Center.

- In een tegel op het **overzicht** van Security Center (hoofd dashboard):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="De beveiligde Score op het dash board van de Security Center":::

- Op de pagina speciale **beveiligde Score** ziet u de beveiligde score voor uw abonnement en uw beheer groepen:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="De beveiligde score voor abonnementen op de pagina beveiligde Score van Security Center":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="De beveiligde score voor beheer groepen op de pagina beveiligde Score van Security Center":::

    > [!NOTE]
    > Alle beheer groepen waarvoor u onvoldoende machtigingen hebt, worden weer gegeven als ' beperkt '. 

- Boven aan de pagina **aanbevelingen** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="De pagina met aanbevelingen voor beveiligde scores op Security Center":::

### <a name="get-your-secure-score-from-the-rest-api"></a>Uw beveiligde Score ophalen uit de REST API

U krijgt toegang tot uw score via de API voor beveiligde scores. De API-methoden bieden de flexibiliteit om query's uit te voeren op de gegevens en uw eigen rapportagemechanisme te bouwen van uw beveiligingsscores in de loop van de tijd. U kunt bijvoorbeeld de [API beveiligde scores](/rest/api/securitycenter/securescores) gebruiken om de score voor een specifiek abonnement op te halen. Daarnaast kunt u de [API besturings elementen voor beveiligde scores](/rest/api/securitycenter/securescorecontrols) gebruiken om de beveiligings controles en de huidige Score van uw abonnementen weer te geven.

![Het ophalen van een enkele beveiligde Score via de API](media/secure-score-security-controls/single-secure-score-via-api.png)

Zie voor voor beelden van hulpprogram ma's die zijn gebouwd boven op de API voor beveiligde scores [het beveiligde Score gebied van onze github-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Uw beveiligde Score ophalen uit de Azure-resource grafiek (ARG)

Azure resource Graph biedt directe toegang tot resource gegevens in uw Cloud omgevingen met krachtige filters, groeperingen en sorteer mogelijkheden. Het is een snelle en efficiënte manier om via programma code of vanuit de Azure Portal informatie op te vragen over Azure-abonnementen. [Meer informatie over Azure Resource Graph](../governance/resource-graph/index.yml).

Om toegang te krijgen tot de beveiligde score voor meerdere abonnementen met ARG:

1. Open in de Azure Portal **Azure resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="De pagina aanbeveling van Azure resource Graph Explorer * * wordt gestart" :::

1. Voer uw Kusto-query in (met behulp van de voor beelden hieronder voor hulp).

    - Met deze query worden de abonnements-ID, de huidige score in punten en als een percentage en de maximale score voor het abonnement geretourneerd. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Met deze query wordt de status van alle beveiligings controles geretourneerd. Voor elk besturings element krijgt u het aantal slechte resources, de huidige score en de maximale score. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selecteer **query uitvoeren**.




## <a name="tracking-your-secure-score-over-time"></a>Uw beveiligde Score na verloop van tijd bijhouden

Als u een Power BI gebruiker bent met een Pro-account, kunt u de **beveiligde Score gedurende een periode** Power bi dash board gebruiken om uw beveiligde Score na verloop van tijd bij te houden en eventuele wijzigingen te onderzoeken.

> [!TIP]
> U kunt dit dash board vinden, evenals andere hulp middelen voor het werken met een beveiligde Score, in het toegewezen gebied van de Azure Security Center Community op GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Het dash board bevat de volgende twee rapporten die u helpen bij het analyseren van de beveiligings status:

- **Overzicht van resources** : bevat samengevatte gegevens met betrekking tot de status van uw resources.
- **Overzicht van beveiligde scores** : bevat samengevatte gegevens over de voortgang van uw score. Gebruik de grafiek beveiligde Score over tijd per abonnement om de wijzigingen in de Score weer te geven. Als u een aanzienlijke wijziging in uw score ziet, raadpleegt u de tabel met gedetecteerde wijzigingen die van invloed kunnen zijn op uw beveiligde score voor mogelijke wijzigingen die de wijziging zouden hebben veroorzaakt. Deze tabel bevat verwijderde resources, nieuw geïmplementeerde resources of bronnen waarvan de beveiligings status is gewijzigd voor een van de aanbevelingen.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="De optionele beveiligde Score in de loop van de tijd Power BI dash board voor het bijhouden van uw beveiligde Score over tijd en het onderzoeken van wijzigingen":::





## <a name="how-your-secure-score-is-calculated"></a>Hoe uw beveiligde score wordt berekend 

De bijdrage van elk beveiligings beheer voor de algehele beveiligde score wordt duidelijk weer gegeven op de pagina aanbevelingen.

[![De verbeterde beveiligde Score introduceert beveiligings controles](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Om alle mogelijke punten voor een beveiligings controle op te halen, moeten al uw resources voldoen aan alle beveiligings aanbevelingen binnen het beveiligings beheer. Security Center heeft bijvoorbeeld meerdere aanbevelingen over hoe u uw beheer poorten kunt beveiligen. U moet deze allemaal herstellen om een verschil te maken met uw beveiligde Score.

Het beveiligings beheer met de naam ' systeem updates Toep assen ' heeft bijvoorbeeld een maximum Score van zes punten, die u in de tooltip kunt zien op de mogelijke verhogings waarde van het besturings element:

[![Het beveiligings beheer ' systeem updates Toep assen '](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

De maximum score voor dit besturings element, het Toep assen van systeem updates, is altijd 6. In dit voor beeld zijn er 50 resources. Daarom delen we de maximale score met 50 en het resultaat is dat elke resource 0,12 punten bijdraagt. 

* **Mogelijke verhoging** (0,12 x 8 beschadigde resources = 0,96): de resterende punten die voor u beschikbaar zijn in het besturings element. Als u alle aanbevelingen in dit besturings element herstelt, neemt uw score toe met 2% (in dit geval 0,96 punten afgerond tot op 1 punt). 
* **Huidige Score** (0,12 x 42 gezonde resources = 5,04): de huidige score voor dit besturings element. Elk besturings element draagt bij aan de totale score. In dit voor beeld draagt het besturings element 5,04 punten met het huidige beveiligde totaal.
* **Maximale score** -het maximum aantal punten dat u kunt krijgen door alle aanbevelingen binnen een besturings element te volt ooien. De maximum score voor een besturings element duidt op de relatieve betekenis van dat besturings element. Gebruik de maximale score waarden om de problemen te sorteren die het eerst moeten worden uitgevoerd. 


### <a name="calculations---understanding-your-score"></a>Berekeningen: uitleg van uw Score

|Metrisch|Formule en voor beeld|
|-|-|
|**Huidige Score van beveiligings beheer**|<br>![Vergelijking voor het berekenen van de Score van een beveiligings controle](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Elk afzonderlijk beveiligings beheer draagt bij aan de beveiligings Score. Elke resource die wordt beïnvloed door een aanbeveling binnen het besturings element, draagt bij aan de huidige Score van het besturings element. De huidige score voor elk besturings element is een meting van de status van de resources *in* het besturings element.<br>![Knop info met de waarden die worden gebruikt bij het berekenen van de huidige Score van het beveiligings beheer](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In dit voor beeld wordt de maximale Score van 6 gedeeld door 78, omdat dat de som is van de in orde zijnde en slechte resources.<br>6/78 = 0,0769<br>Als u wilt vermenigvuldigen met het aantal ongezonde resources (4), resulteert dit in de huidige Score:<br>0,0769 * 4 = **0,31**<br><br>|
|**Beveiligingsscore**<br>Enkel abonnement|<br>![Vergelijking voor het berekenen van de beveiligde Score van een abonnement](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Een beveiligde Score van één abonnement waarbij alle besturings elementen zijn ingeschakeld](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In dit voor beeld is er één abonnement met alle beveiligings controles beschikbaar (een potentiële maximum Score van 60 punten). De score toont 28 punten van een mogelijke 60 en de resterende 32 punten worden weer gegeven in de sectie ' potentiële Score verhogen ' van de beveiligings controles.<br>![Lijst met besturings elementen en de potentiële toename van de Score](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Beveiligingsscore**<br>Meerdere abonnementen|<br>![Vergelijking voor het berekenen van de beveiligde score voor meerdere abonnementen](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Bij het berekenen van de gecombineerde score voor meerdere abonnementen, bevat Security Center een *gewicht* voor elk abonnement. De relatieve gewichten voor uw abonnementen worden bepaald door Security Center op basis van factoren zoals het aantal resources.<br>De huidige score voor elk abonnement wordt berekend op dezelfde manier als voor één abonnement, maar vervolgens wordt het gewicht toegepast, zoals weer gegeven in de vergelijking.<br>Wanneer er meerdere abonnementen worden weer gegeven, evalueert de beveiligde score alle resources binnen het ingeschakelde beleid en worden de gecombineerde impact van elk van de maximale scores van het beveiligings beheer gegroepeerd.<br>![Beveiligde score voor meerdere abonnementen waarbij alle besturings elementen zijn ingeschakeld](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>De gecombineerde score is **geen** gemiddelde. in plaats daarvan is het de geëvalueerde postuur van de status van alle resources in alle abonnementen.<br>Ook als u naar de pagina aanbevelingen gaat en de potentiële punten die beschikbaar zijn, opneemt, zult u merken dat het verschil tussen de huidige Score (24) en de Maxi maal beschik bare Score (60) is.|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Welke aanbevelingen zijn opgenomen in de berekeningen van de veilige Score?

Alleen ingebouwde aanbevelingen hebben invloed op de beveiligde Score.

Aanbevelingen die als **Preview** zijn gemarkeerd, zijn niet opgenomen in de berekeningen van uw beveiligde Score. Ze moeten, waar mogelijk, nog steeds worden hersteld. Wanneer de preview-periode is afgelopen, worden ze dus meegerekend in uw score.

Een voorbeeld van een preview-aanbeveling:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Aanbeveling met de preview-markering":::

## <a name="improve-your-secure-score"></a>Uw secure score verbeteren

Als u uw beveiligde score wilt verbeteren, kunt u de aanbevelingen voor beveiliging herstellen in uw lijst met aanbevelingen. U kunt elke aanbeveling hand matig herstellen voor elke resource, of met behulp van de **oplossing voor snel oplossen!** optie (indien beschikbaar) om een herbemiddeling toe te passen voor een aanbeveling voor een groep resources snel. Zie [aanbevelingen herstellen](security-center-remediate-recommendations.md)voor meer informatie.

Een andere manier om uw score te verbeteren en ervoor te zorgen dat uw gebruikers geen resources maken die een negatieve invloed hebben op uw score, is door de opties voor afdwingen en weigeren te configureren voor de relevante aanbevelingen. Meer informatie vindt u in [Onjuiste configuraties voorkomen met de aanbevelingen voor afdwingen/weigeren](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Beveiligings controles en hun aanbevelingen

De volgende tabel bevat de beveiligings opties in Azure Security Center. Voor elk besturings element ziet u het maximum aantal punten dat u aan uw beveiligde Score kunt toevoegen als u *alle* aanbevelingen die worden vermeld in het besturings element herstelt, voor *al* uw resources. 

De set met beveiligings aanbevelingen die worden meegeleverd met Security Center is afgestemd op de beschik bare resources in de omgeving van elke organisatie. De aanbevelingen kunnen verder worden aangepast door [beleid uit te scha kelen](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) en [specifieke bronnen uit te sluiten van een aanbeveling](exempt-resource.md). 
 
We raden u aan elke organisatie de toegewezen Azure Policy-initiatieven zorgvuldig te controleren. 

> [!TIP]
> Zie [werken met beveiligings beleid](tutorial-security-policy.md)voor meer informatie over het controleren en bewerken van uw initiatieven. 

Hoewel het standaard Security Initiative van Security Center is gebaseerd op de best practices en standaarden van de branche, zijn er scenario's waarin de hieronder vermelde ingebouwde aanbevelingen mogelijk niet volledig in uw organisatie passen. Daarom is het soms nood zakelijk om het standaard initiatief te corrigeren, zonder dat dit de veiligheid in gevaar heeft, zodat het wordt afgestemd op het eigen beleid van uw organisatie. industrie normen, regelgevings normen en benchmarks waarmee u moet voldoen.<br><br>
<div class="foo">

<style type="text/css"> . TG {Border-samen vouwen: samen vouwen; rand-afstand: 0;}. TG TD {Border-Color: zwart; rand stijl: effen; rand breedte: 1px; letter type-serie: Arial, Sans-Serif; letter type: 14px; overflow: verborgen; opvulling: 10px 5px; woord-onderbreking: normaal;}. TG th {Border-Color: Black; rand-Style: Solid; rand breedte: 1px; lettertype familie: Arial, Sans-Serif; letter type: 18px; letter type-Weight: normaal; overflow: verborgen; opvulling: 10px 5px; woord-onderbroken: Normal;}. tg. TG: cly1 {tekst uitlijning: links; verticaal uitlijnen: midden}. tg. TG-lboi {Border-Color: overnemen; tekst uitlijning: links; verticaal uitlijnen: Midden} </style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Beveiligings controle, Score en beschrijving</b><br></th>
    <th class="tg-cly1"><b>Aanbevelingen</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">MFA inschakelen (maximum score 10)</p></strong>Als u alleen een wacht woord gebruikt om een gebruiker te verifiëren, wordt een aanvals vector geopend. Als het wacht woord zwak is of elders is weer gegeven, is het echt de gebruiker die zich aanmeldt met de gebruikers naam en het wacht woord?<br>Als <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> is ingeschakeld, zijn uw accounts veiliger en kunnen gebruikers zich nog steeds verifiëren bij bijna elke toepassing met eenmalige aanmelding (SSO).</td>
    <td class="tg-lboi"; width=55%>- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement<br>- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Poorten voor beveiligde beheer (maximale score 8)</p></strong>Met beveiligings aanvallen kunt u toegang krijgen tot een virtuele machine met bedoelings beheer poorten. Aangezien de poorten niet altijd open hoeven te zijn, is een beperkings strategie het verminderen van de bloot stelling aan de poorten met Just-in-time-netwerk toegangs beheer, netwerk beveiligings groepen en poort beheer voor virtuele machines.<br>Omdat veel IT-organisaties geen SSH-communicatie van hun netwerk blok keren, kunnen aanvallers versleutelde tunnels maken waarmee RDP-poorten op geïnfecteerde systemen kunnen communiceren met de opdracht van de aanvaller om servers te beheren. Kwaadwillende personen kunnen het Windows-subsysteem voor extern beheer gebruiken om zich op een later tijdstip in uw omgeving te verplaatsen en gestolen referenties te gebruiken om toegang te krijgen tot andere bronnen in een netwerk.</td>
    <td class="tg-lboi"; width=55%>- Beheer poorten van virtuele machines moeten worden beveiligd met Just-in-time-netwerk toegangs beheer<br>- Virtuele machines moeten worden gekoppeld aan een netwerk beveiligings groep<br>- Beheer poorten moeten worden gesloten op uw virtuele machines</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Systeem updates Toep assen (maximum score 6)</p></strong>Systeem updates bieden organisaties de mogelijkheid om operationele efficiëntie te hand haven, beveiligings problemen te verminderen en een stabielere omgeving te bieden voor eind gebruikers. Als updates niet worden toegepast, worden niet-opgeloste beveiligings problemen en resultaten in omgevingen beschreven die vatbaar zijn voor aanvallen. Deze beveiligings problemen kunnen worden misbruikt en leiden tot gegevens verlies, gegevens exfiltration, Ransomware en misbruik van bronnen. Voor het implementeren van systeem updates kunt u de <a href="/azure/automation/update-management/overview">updatebeheer-oplossing gebruiken om patches en updates</a> voor uw virtuele machines te beheren. Update beheer is het proces van het beheren van de implementatie en het onderhoud van software releases.</td>
    <td class="tg-lboi"; width=55%>- Controle agent status problemen moeten worden opgelost op uw computers<br>- Bewakings agent moet worden geïnstalleerd op virtuele-machine schaal sets<br>- Bewakings agent moet op uw computers zijn geïnstalleerd<br>- De versie van het besturings systeem moet worden bijgewerkt voor uw Cloud service rollen<br>- Systeem updates op virtuele-machine schaal sets moeten worden geïnstalleerd<br>- Systeem updates moeten worden geïnstalleerd op uw computers<br>- Uw computers moeten opnieuw worden opgestart om systeem updates toe te passen<br>- Kubernetes Services moet worden bijgewerkt naar een niet-kwets bare Kubernetes-versie<br>- Bewakings agent moet worden geïnstalleerd op uw virtuele machines<br>- Log Analytics agent moet worden geïnstalleerd op uw op Windows gebaseerde Azure Arc-machines (preview)<br>- Log Analytics agent moet worden geïnstalleerd op uw op Linux gebaseerde Azure-Arc-machines (preview)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Beveiligings problemen oplossen (maximale score 6)</p></strong>Een beveiligingslek is een zwakke plek die een bedreigings actor zou kunnen gebruiken om de vertrouwelijkheid, Beschik baarheid of integriteit van een resource in gevaar te brengen. Het <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">beheren van beveiligings problemen</a> vermindert de bloot stelling van de organisatie, de beveiligingen endpoint Surface Area, verhoogt de organisatie tolerantie en vermindert de kwets baarheid van uw resources. Het beheer van dreigingen en zwakke plekken biedt inzicht in software en beveiligings configuraties en geeft aanbevelingen voor oplossingen.</td>
    <td class="tg-lboi"; width=55%>- Geavanceerde gegevens beveiliging moet zijn ingeschakeld op SQL Database<br>- Beveiligings problemen in Azure Container Registry installatie kopieën moeten worden hersteld<br>- Beveiligings problemen voor uw SQL-data bases moeten worden hersteld<br>- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken<br>- De evaluatie van beveiligings problemen moet worden ingeschakeld op het SQL-beheerde exemplaar<br>- De evaluatie van beveiligings problemen moet worden ingeschakeld op uw SQL-servers<br>- De oplossing voor de evaluatie van beveiligings problemen moet worden geïnstalleerd op uw virtuele machines<br>- Container installatie kopieën moeten alleen worden geïmplementeerd vanuit vertrouwde registers (preview)<br>- Azure Policy-invoeg toepassing voor Kubernetes moet zijn geïnstalleerd en ingeschakeld in uw clusters (preview)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Versleuteling inschakelen op rest (maximum score 4)</p></strong><a href="/azure/security/fundamentals/encryption-atrest">Versleuteling bij rest</a> biedt gegevens beveiliging voor opgeslagen gegevens. Aanvallen tegen gegevens in rust zijn onder andere pogingen om fysieke toegang te krijgen tot de hardware waarop de gegevens zijn opgeslagen. Azures gebruiken symmetrische versleuteling om grote hoeveel heden gegevens op rest te versleutelen en ontsleutelen. Een symmetrische versleutelings sleutel wordt gebruikt voor het versleutelen van gegevens die naar opslag worden geschreven. Deze versleutelings sleutel wordt ook gebruikt om die gegevens te ontsleutelen wanneer deze gereed zijn voor gebruik in het geheugen. Sleutels moeten worden opgeslagen op een veilige locatie met toegangs beheer op basis van identiteit en controle beleid. Een dergelijke veilige locatie is Azure Key Vault. Als een aanvaller de versleutelde gegevens verkrijgt, maar niet de versleutelings sleutels, heeft de aanvaller geen toegang tot de gegevens zonder de versleuteling te verbreken.</td>
    <td class="tg-lboi"; width=55%>- Schijf versleuteling moet worden toegepast op virtuele machines<br>- Transparent Data Encryption op SQL Database moet zijn ingeschakeld<br>- De variabelen van het Automation-account moeten worden versleuteld<br>- Voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign<br>- SQL Server TDE-beveiliging moet worden versleuteld met uw eigen sleutel</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Gegevens in transit versleutelen (maximum score 4)</p></strong>Gegevens zijn in transit wanneer ze worden verzonden tussen onderdelen, locaties of Program ma's. Organisaties die gegevens in de overdracht niet kunnen beveiligen, zijn vatbaar voor man-in-the-middle-aanvallen, afluis teren en het overnemen van sessies. SSL/TLS-protocollen moeten worden gebruikt voor het uitwisselen van gegevens en een VPN wordt aanbevolen. Bij het verzenden van versleutelde gegevens tussen een virtuele machine van Azure en een on-premises locatie via internet kunt u een virtuele netwerk gateway zoals <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN gateway</a> gebruiken om versleuteld verkeer te verzenden.</td>
    <td class="tg-lboi"; width=55%>- De API-app mag alleen toegankelijk zijn via HTTPS<br>- functie-app mag alleen toegankelijk zijn via HTTPS<br>- Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld<br>- Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld<br>- Webtoepassing mag alleen toegankelijk zijn via HTTPS<br>- Het persoonlijke eind punt moet zijn ingeschakeld voor PostgreSQL-servers<br>- SSL-verbinding afdwingen moet zijn ingeschakeld voor PostgreSQL-database servers<br>- SSL-verbinding afdwingen moet zijn ingeschakeld voor MySQL-database servers<br>- TLS moet worden bijgewerkt naar de nieuwste versie van uw API-app<br>- TLS moet worden bijgewerkt naar de nieuwste versie voor uw functie-app<br>- TLS moet worden bijgewerkt naar de nieuwste versie van uw web-app<br>- FTPS moet zijn vereist in uw API-app<br>- FTPS moet zijn vereist in uw functie-app<br>- FTPS moet vereist zijn in uw web-app</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Toegang en machtigingen beheren (maximale score 4)</p></strong>Een kern onderdeel van een beveiligings programma zorgt ervoor dat uw gebruikers de benodigde toegang hebben om hun werk te doen, maar niet meer dan dat: het model voor de <a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">minimale bevoegdheids toegang</a>.<br>Beheer de toegang tot uw resources door roltoewijzingen te maken met behulp van <a href="/azure/role-based-access-control/overview">Azure op rollen gebaseerd toegangs beheer (Azure RBAC)</a>. Een roltoewijzing bestaat uit drie elementen:<br>- <strong>Beveiligingsprincipal</strong>: het object waartoe de gebruiker toegang vraagt<br>- <strong>Roldefinitie</strong>: hun machtigingen<br>- <strong>Bereik</strong>: de set resources waarop de machtigingen van toepassing zijn</td>
    <td class="tg-lboi"; width=55%>- Afgeschafte accounts moeten worden verwijderd uit uw abonnement (preview)<br>- Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement (preview)<br>- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement (preview)<br>- Externe accounts met schrijf machtigingen moeten worden verwijderd uit uw abonnement (preview)<br>- Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen<br>- Toegangs beheer op basis van rollen (Azure RBAC) van Azure moet worden gebruikt voor Kubernetes-Services (preview)<br>- Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie<br>- Service-principals moeten worden gebruikt voor het beveiligen van uw abonnementen in plaats van beheer certificaten<br>- Er moeten mini maal bevoegde Linux-mogelijkheden worden afgedwongen voor containers (preview-versie)<br>- Onveranderbaar (alleen-lezen) hoofd bestands systeem moet worden afgedwongen voor containers (preview-versie)<br>- Container met bevoegdheids escalatie moet worden vermeden (preview-versie)<br>- Containers uitvoeren als hoofd gebruiker moet worden vermeden (preview-versie)<br>- Containers die gevoelige host-naam ruimten delen, moeten worden vermeden (preview-versie)<br>- Het gebruik van pod HostPath-volume koppelingen moet worden beperkt tot een bekende lijst (preview-versie)<br>- Geprivilegieerde containers moeten worden vermeden (preview-versie)<br>- Azure Policy-invoeg toepassing voor Kubernetes moet zijn geïnstalleerd en ingeschakeld in uw clusters (preview)<br>- Beheerde identiteit moet worden gebruikt in uw API-app<br>- De beheerde identiteit moet worden gebruikt in uw functie-app<br>- De beheerde identiteit moet worden gebruikt in uw web-app</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Beveiligings configuraties herstellen (maximum score 4)</p></strong>Onjuist geconfigureerde IT-assets hebben een hoger risico op aanvallen. Basis acties voor beveiliging worden vaak verg eten wanneer er activa worden geïmplementeerd en er deadlines aan moeten worden voldaan. Onjuiste configuratie van beveiliging kan elk niveau in de infra structuur zijn: van de besturings systemen en netwerk apparaten tot cloud resources.<br>Azure Security Center vergelijkt voortdurend de configuratie van uw resources met vereisten in de industrie normen,-voor schriften en-benchmarks. Wanneer u de relevante ' nalevings pakketten ' (standaarden en basis lijnen) hebt geconfigureerd die van toepassing zijn op uw organisatie, zullen eventuele hiaten resulteren in beveiligings aanbevelingen die de CCEID omvatten en een uitleg van de potentiële beveiligings impact.<br>Veelgebruikte pakketten zijn <a href="/azure/security/benchmarks/introduction">Azure Security Bench Mark</a> en <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure basis Bench Mark-benchmark versie 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- Beveiligings problemen in container beveiligings configuraties moeten worden hersteld<br>- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld<br>- Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld<br>- Bewakings agent moet worden geïnstalleerd op uw virtuele machines<br>- Bewakings agent moet op uw computers zijn geïnstalleerd<br>- Log Analytics agent moet worden geïnstalleerd op uw op Windows gebaseerde Azure Arc-machines (preview)<br>- Log Analytics agent moet worden geïnstalleerd op uw op Linux gebaseerde Azure-Arc-machines (preview)<br>- Bewakings agent moet worden geïnstalleerd op virtuele-machine schaal sets<br>- Controle agent status problemen moeten worden opgelost op uw computers<br>- Het negeren of uitschakelen van containers AppArmor profiel moet worden beperkt (preview-versie)<br>- Azure Policy-invoeg toepassing voor Kubernetes moet zijn geïnstalleerd en ingeschakeld in uw clusters (preview)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Onbevoegde netwerk toegang beperken (maximale score 4)</p></strong>Eind punten in een organisatie bieden een directe verbinding van uw virtuele netwerk naar ondersteunde Azure-Services. Virtuele machines in een subnet kunnen communiceren met alle resources. Als u de communicatie van en naar resources binnen een subnet wilt beperken, maakt u een netwerk beveiligings groep en koppelt u deze aan het subnet. Organisaties kunnen het aantal niet-geautoriseerde verkeer beperken en beveiligen door binnenkomende en uitgaande regels te maken.</td>
    <td class="tg-lboi"; width=55%>- Door sturen via IP op uw virtuele machine moet worden uitgeschakeld<br>- Geautoriseerde IP-bereiken moeten worden gedefinieerd in Kubernetes Services (preview)<br>- KEUR Toegang tot App Services moet worden beperkt (preview-versie)<br>- KEUR De regels voor webtoepassingen op IaaS Nsg's moeten worden gehard<br>- Virtuele machines moeten worden gekoppeld aan een netwerk beveiligings groep<br>- CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw API-app<br>- CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw functie-app<br>- CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw webtoepassing<br>- Fout opsporing op afstand moet worden uitgeschakeld voor de API-app<br>- Fout opsporing op afstand moet worden uitgeschakeld voor functie-app<br>- Externe fout opsporing moet worden uitgeschakeld voor webtoepassing<br>- Toegang moet worden beperkt voor strikte netwerk beveiligings groepen met Internet gerichte Vm's<br>- Aanbevelingen voor adaptieve netwerk beveiliging moeten worden toegepast op Internet gerichte virtuele machines<br>- Azure Policy-invoeg toepassing voor Kubernetes moet zijn geïnstalleerd en ingeschakeld in uw clusters (preview)<br>- Containers moeten worden geluisterd op toegestane poorten (preview-versie)<br>- Services mogen alleen op toegestane poorten Luis teren (preview)<br>- Het gebruik van hostnetwerkadapters en poorten moet worden beperkt (preview-versie)<br>- Virtuele netwerken moeten worden beveiligd door Azure Firewall (preview-versie)<br>- Het persoonlijke eind punt moet zijn ingeschakeld voor MariaDB-servers<br>- Het persoonlijke eind punt moet worden ingeschakeld voor MySQL-servers<br>- Het persoonlijke eind punt moet zijn ingeschakeld voor PostgreSQL-servers</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Adaptief toepassings beheer Toep assen (maximum score 3)</p></strong>Adaptief toepassings beheer (AAC) is een intelligente, geautomatiseerde, end-to-end oplossing, waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Azure-en niet-Azure-machines. Het helpt ook om uw computers te beschermen tegen schadelijke software.<br>Security Center gebruikt machine learning om een lijst met bekende veilige toepassingen voor een groep machines te maken.<br>Deze innovatieve benadering van de lijst met goedgekeurde toepassingen biedt de beveiligings voordelen zonder de beheer complexiteit.<br>AAC is met name relevant voor speciaal ontwikkelde servers die een specifieke set toepassingen moeten uitvoeren.</td>
    <td class="tg-lboi"; width=55%>- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines<br>- Bewakings agent moet worden geïnstalleerd op uw virtuele machines<br>- Bewakings agent moet op uw computers zijn geïnstalleerd<br>- Log Analytics agent moet worden geïnstalleerd op uw op Windows gebaseerde Azure Arc-machines (preview)<br>- Log Analytics agent moet worden geïnstalleerd op uw op Linux gebaseerde Azure-Arc-machines (preview)<br>- Controle agent status problemen moeten worden opgelost op uw computers</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Gegevens classificatie Toep assen (maximum score 0)</p></strong>Door de gegevens van uw organisatie te classificeren door middel van gevoeligheid en bedrijfs impact kunt u de waarde bepalen en toewijzen aan de gegevens en de strategie en basis voor governance bieden.<br><a href="/azure/information-protection/what-is-information-protection">Azure Information Protection</a> kunnen helpen bij het classificeren van gegevens. Het maakt gebruik van versleuteling, identiteit en autorisatie beleid om gegevens te beveiligen en gegevens toegang te beperken. Sommige classificaties die micro soft gebruikt, zijn niet-zakelijk, openbaar, algemeen, vertrouwelijk en zeer vertrouwelijk.</td>
    <td class="tg-lboi"; width=55%>- Gevoelige gegevens in uw SQL-data bases moeten worden geclassificeerd</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Toepassingen beveiligen tegen DDoS-aanvallen (maximum score 2)</p></strong>Gedistribueerde Denial-of-service-aanvallen (DDoS) zorgen voor bronnen en renderen toepassingen onbruikbaar. Gebruik <a href="/azure/virtual-network/ddos-protection-overview">Azure DDoS Protection standaard</a> om uw organisatie te beschermen tegen de drie belangrijkste typen DDoS-aanvallen:<br>- <strong>Volumetrische aanvallen</strong> flooden het netwerk met legitiem verkeer. DDoS Protection standaard verkleint deze aanvallen door ze automatisch te absorberen of te reinigen.<br>- <strong>Protocol aanvallen</strong> genereren een doel dat niet toegankelijk is door zwakke plekken in de laag 3-en laag 4-protocol stack te exploiteren. DDoS Protection standaard verkleint deze aanvallen door schadelijk verkeer te blok keren.<br>- <strong>Resource (toepassing) laag aanvallen</strong> doel webtoepassing pakketten. Tegen dit type beschermen met een Web Application Firewall en DDoS Protection Standard.</td>
    <td class="tg-lboi"; width=55%>- DDoS Protection standaard moet zijn ingeschakeld<br>- De CPU-en geheugen limieten van de container moeten worden afgedwongen (preview-versie)<br>- Azure Policy-invoeg toepassing voor Kubernetes moet zijn geïnstalleerd en ingeschakeld in uw clusters (preview)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Endpoint Protection inschakelen (maximum score 2)</p></strong>Om ervoor te zorgen dat uw eind punten worden beschermd tegen malware, verzamelen en verwerken de gedrags sensors gegevens van de besturings systemen van uw eind punten en verzenden deze gegevens naar de privécloud voor analyse. Security Analytics maakt gebruik van Big Data, machine learning en andere bronnen om reacties op bedreigingen te aanbevelen. <a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Micro soft Defender ATP</a> maakt bijvoorbeeld gebruik van bedreigings informatie om aanvals methoden te identificeren en beveiligings waarschuwingen te genereren.<br>Security Center ondersteunt de volgende eindpunt beveiligings oplossingen: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v 12.1.1.1100, McAfee V10 toevoegen voor Windows, McAfee V10 toevoegen voor Linux en Sophos v9 voor Linux. Als Security Center een van deze oplossingen detecteert, wordt de aanbeveling om Endpoint Protection te installeren niet meer weer gegeven.</td>
    <td class="tg-lboi"; width=55%>- Endpoint Protection-status fouten moeten worden hersteld op schaal sets voor virtuele machines<br>- Endpoint Protection-status problemen moeten worden opgelost op uw computers<br>- Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machine schaal sets<br>- Endpoint Protection-oplossing installeren op virtuele machines<br>- Controle agent status problemen moeten worden opgelost op uw computers<br>- Bewakings agent moet worden geïnstalleerd op virtuele-machine schaal sets<br>- Bewakings agent moet op uw computers zijn geïnstalleerd<br>- Bewakings agent moet worden geïnstalleerd op uw virtuele machines<br>- Log Analytics agent moet worden geïnstalleerd op uw op Windows gebaseerde Azure Arc-machines (preview)<br>- Log Analytics agent moet worden geïnstalleerd op uw op Linux gebaseerde Azure-Arc-machines (preview)<br>- Endpoint Protection-oplossing op uw computers installeren</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Controle en logboek registratie inschakelen (maximum score 1)</p></strong>Logboek gegevens bieden inzicht in problemen in het verleden, waardoor de prestaties van de toepassing kunnen worden verbeterd, en biedt de mogelijkheid om acties te automatiseren die anders hand matig zijn.<br>- <strong>Controle-en beheer logboeken</strong> bieden informatie over <a href="/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> bewerkingen.<br>- <strong>Gegevens vlak logboeken</strong> bevatten informatie over gebeurtenissen die worden gegenereerd als onderdeel van het gebruik van Azure-resources.<br>- <strong>Verwerkte gebeurtenissen</strong> bieden informatie over geanalyseerde gebeurtenissen/waarschuwingen die zijn verwerkt.</td>
    <td class="tg-lboi"; width=55%>- Controle op SQL Server moet zijn ingeschakeld<br>- Diagnostische logboeken in App Services moeten worden ingeschakeld<br>- Diagnostische logboeken in Azure Data Lake Store moeten worden ingeschakeld<br>- Diagnostische logboeken in Azure Stream Analytics moeten worden ingeschakeld<br>- Diagnostische logboeken in batch-accounts moeten worden ingeschakeld<br>- Diagnostische logboeken in Data Lake Analytics moeten worden ingeschakeld<br>- Diagnostische logboeken in Event hub moeten worden ingeschakeld<br>- Diagnostische logboeken in IoT Hub moeten worden ingeschakeld<br>- Diagnostische logboeken in Key Vault moeten worden ingeschakeld<br>- Diagnostische logboeken in Logic Apps moeten worden ingeschakeld<br>- Diagnostische logboeken in de zoek service moeten worden ingeschakeld<br>- Diagnostische logboeken in Service Bus moeten worden ingeschakeld<br>- Diagnostische logboeken in Virtual Machine Scale Sets moeten worden ingeschakeld<br>- Metrische waarschuwings regels moeten worden geconfigureerd voor batch-accounts<br>- Voor de SQL-controle-instellingen moet Action-Groups zijn geconfigureerd voor het vastleggen van kritieke activiteiten<br>- SQL-servers moeten worden geconfigureerd met controle dagen van meer dan 90 dagen.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Advanced Threat Protection inschakelen (maximale score 0)</p></strong>De optionele Azure Defender Threat Protection-abonnementen van Azure Security Center bieden uitgebreide beveiligingen voor uw omgeving. Wanneer Security Center een bedreiging in een wille keurig gebied van uw omgeving detecteert, wordt er een waarschuwing gegenereerd. Deze waarschuwingen bevatten details over de relevante bronnen, voorgestelde stappen voor herstel en soms ook de optie om een logische app te activeren.<br>Elk Azure Defender-abonnement is een afzonderlijke, optionele aanbieding die u kunt inschakelen met behulp van de relevante aanbeveling in dit beveiligings beheer.<br>Meer <a href="/azure/security-center/threat-protection">informatie over beveiliging tegen bedreigingen vindt u in Security Center</a>.</td>
    <td class="tg-lboi"; width=55%>- Geavanceerde gegevens beveiliging moet zijn ingeschakeld op Azure SQL Database servers<br>- Geavanceerde gegevens beveiliging moet zijn ingeschakeld op SQL-servers op computers<br>- Geavanceerde beveiliging tegen bedreigingen moet zijn ingeschakeld op Virtual Machines<br>- Geavanceerde beveiliging tegen bedreigingen moet zijn ingeschakeld op Azure App Service plannen<br>- Geavanceerde beveiliging tegen bedreigingen moet zijn ingeschakeld voor Azure Storage accounts<br>- Geavanceerde beveiliging tegen bedreigingen moet zijn ingeschakeld op Azure Kubernetes Service-clusters<br>- Geavanceerde beveiliging tegen bedreigingen moet worden ingeschakeld voor Azure Container Registry registers<br>- Geavanceerde beveiliging tegen bedreigingen moet zijn ingeschakeld op Azure Key Vault kluizen</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Best practices voor beveiliging implementeren (maximale score 0)</p></strong>Bij moderne beveiligings procedures wordt ervan uitgegaan dat de netwerk verbinding wordt geschonden. Daarom zijn veel van de best practices in dit besturings element gericht op het beheren van identiteiten.<br>Het verlies van sleutels en referenties is een veelvoorkomend probleem. <a href="/azure/key-vault/key-vault-overview">Azure Key Vault</a> beschermt sleutels en geheimen door sleutels, pfx-bestanden en wacht woorden te versleutelen.<br>Virtuele particuliere netwerken (Vpn's) vormen een veilige manier om toegang te krijgen tot uw virtuele machines. Als er Vpn's niet beschikbaar zijn, kunt u gebruikmaken van complexe wachtwoord zinnen en twee ledige verificatie, zoals <a href="/azure/active-directory/authentication/concept-mfa-howitworks">Azure AD multi-factor Authentication</a>. Met twee ledige verificatie wordt voor komen dat de zwakke punten inherent zijn aan het vertrouwen van gebruikers namen en wacht woorden.<br>Het gebruik van sterke verificatie-en autorisatie platforms is een andere best practice. Door gebruik te maken van federatieve identiteiten kunnen organisaties het beheer van geautoriseerde identiteiten delegeren. Dit is ook belang rijk wanneer werk nemers worden beëindigd en hun toegang moet worden ingetrokken.</td>
    <td class="tg-lboi"; width=55%>- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement<br>- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement<br>- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement<br>- Toegang tot opslag accounts met firewall-en virtuele-netwerk configuraties moet worden beperkt<br>- Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit de Event hub-naam ruimte<br>- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers<br>- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw beheerde instanties<br>- Autorisatie regels voor het event hub-exemplaar moeten worden gedefinieerd<br>- Opslag accounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources<br>- Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources<br>- Subnetten moeten worden gekoppeld aan een netwerk beveiligings groep<br>- Evaluatie Windows exploit Guard moet zijn ingeschakeld <br>- Evaluatie Gast configuratie agent moet worden geïnstalleerd<br>- Niet-Internet gerichte virtuele machines moeten worden beveiligd met netwerk beveiligings groepen<br>- Azure Backup moet zijn ingeschakeld voor virtuele machines<br>- De geo-redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB<br>- De geo-redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL<br>- De geo-redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL<br>- PHP moet worden bijgewerkt naar de nieuwste versie van uw API-app<br>- PHP moet worden bijgewerkt naar de nieuwste versie van uw web-app<br>- Java moet worden bijgewerkt naar de nieuwste versie van uw API-app<br>- Java moet worden bijgewerkt naar de nieuwste versie van uw functie-app<br>- Java moet worden bijgewerkt naar de nieuwste versie van uw web-app<br>- Python moet worden bijgewerkt naar de nieuwste versie van uw API-app<br>- Python moet worden bijgewerkt naar de nieuwste versie voor uw functie-app<br>- Python moet worden bijgewerkt naar de nieuwste versie van uw web-app<br>- Controle retentie voor SQL-servers moet ten minste 90 dagen worden ingesteld<br>- Web-apps moeten een SSL-certificaat aanvragen voor alle inkomende aanvragen</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>Veelgestelde vragen over beveiligde scores

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Als ik slechts drie van de vier aanbevelingen in een beveiligings controle adresseer, wordt mijn beveiligde Score gewijzigd?
Nee. Deze wijziging wordt pas doorgevoerd wanneer u alle aanbevelingen voor één resource herstelt. Als u de maximum score voor een besturings element wilt ophalen, moet u alle aanbevelingen voor alle resources herstellen.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Als een aanbeveling niet van toepassing is en deze in het beleid uitschakelt, wordt mijn beveiligings controle vervuld en mijn beveiligde score bijgewerkt?
Ja. We raden u aan om aanbevelingen uit te scha kelen wanneer ze niet van toepassing zijn in uw omgeving. Zie [beveiligings beleid uitschakelen](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)voor instructies over het uitschakelen van een specifieke aanbeveling.

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Als een beveiligings besturings element me nul punten naar mijn beveiligde Score biedt, moet ik dit dan negeren?
In sommige gevallen ziet u een maximum Score van het besturings element die groter is dan nul, maar de impact is nul. Wanneer de incrementele score voor het oplossen van resources verwaarloosbaar is, wordt deze afgerond op nul. Negeer deze aanbevelingen niet omdat ze nog steeds betere beveiliging bieden. De enige uitzonde ring hierop is het besturings element ' extra best practice '. Door deze aanbevelingen te herstellen, wordt uw score niet verhoogd, maar wordt uw algehele beveiliging verbeterd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel vindt u een beschrijving van de beveiligde Score en de beveiligings controles die worden geïntroduceerd. Raadpleeg de volgende artikelen voor gerelateerd materiaal:

- [Meer informatie over de verschillende elementen van een aanbeveling](security-center-recommendations.md)
- [Meer informatie over het oplossen van aanbevelingen](security-center-remediate-recommendations.md)
- [De GitHub-hulpprogram ma's voor het werken met een beveiligde Score weer geven](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)