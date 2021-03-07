---
title: Adaptieve toepassingsbesturingselementen in Azure Security Center
description: Dit document helpt u bij het gebruik van adaptief toepassings beheer in Azure Security Center om lijst toepassingen toe te staan die worden uitgevoerd op Azure-machines.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: de5c5fbb6673d022517bba2486ed4aa1f739d19b
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439575"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Besturings elementen voor adaptieve toepassingen gebruiken om de kwets bare Opper vlakken van uw computers te verminderen

Meer informatie over de voor delen van de besturings elementen voor adaptieve toepassingen van Azure Security Center en hoe u uw beveiliging kunt verbeteren met behulp van deze, intelligente functie van gegevens.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Wat zijn de adaptieve toepassings besturings elementen van Security Center?

Adaptieve toepassings besturings elementen zijn een intelligente en geautomatiseerde oplossing voor het definiëren van allow-lijsten met bekende veilige toepassingen voor uw machines. 

Organisaties hebben vaak verzamelingen machines die regel matig dezelfde processen uitvoeren. Security Center gebruikt machine learning voor het analyseren van de toepassingen die worden uitgevoerd op uw computers en het maken van een lijst met bekende veilige software. Lijsten toestaan worden gebaseerd op uw specifieke Azure-workloads en u kunt de aanbevelingen verder aanpassen met behulp van de onderstaande instructies.

Wanneer u adaptieve toepassingsregelaars hebt ingeschakeld en geconfigureerd, krijgt u beveiligingswaarschuwingen als er andere toepassingen worden uitgevoerd dan degene die u als veilig hebt gedefinieerd.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Wat zijn de voor delen van adaptieve toepassings besturings elementen?

Door lijsten met bekende veilige toepassingen te definiëren en waarschuwingen te genereren wanneer iets anders wordt uitgevoerd, kunt u meerdere beveiligings doelen bereiken:

- Mogelijke malware identificeren, zelfs als deze niet mogelijk is met antimalware-oplossingen
- Verbeter de naleving van lokale beveiligings beleidsregels waarmee het gebruik van alleen gelicentieerde software wordt bepaald
- Vermijd het uitvoeren van oude of niet-ondersteunde toepassingen
- Voor komen dat specifieke software wordt geblokkeerd door uw organisatie
- Het toezicht verhogen van apps die toegang hebben tot gevoelige gegevens

Er zijn momenteel geen afdwingings opties beschikbaar. Besturings elementen voor adaptieve toepassingen zijn bedoeld om beveiligings waarschuwingen te bieden als een andere toepassing dan de toepassingen die u hebt gedefinieerd als veilig worden uitgevoerd.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|[Azure Defender voor servers](defender-for-servers-introduction.md) is vereist|
|Ondersteunde machines:|![Ja ](./media/icons/yes-icon.png) Azure-en niet-Azure-machines met Windows en Linux<br>![Ja ](./media/icons/yes-icon.png) [Azure Arc](../azure-arc/index.yml) -machines|
|Vereiste rollen en machtigingen:|**Beveiligings lezers** en **lezers** rollen kunnen zowel groepen als de lijsten met bekende veilige toepassingen weer geven<br>**Inzender** -en **beveiligings beheerders** rollen kunnen zowel groepen bewerken als de lijsten met bekende veilige toepassingen|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (Overheid van de VS, China, andere overheden)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Toepassings besturings elementen op een groep machines inschakelen

Als Security Center groepen computers heeft geïdentificeerd in uw abonnementen die consistent een gelijksoortige set toepassingen uitvoeren, wordt u gevraagd de volgende aanbeveling te gebruiken: **adaptieve toepassings besturings elementen voor het definiëren van veilige toepassingen moeten op uw computers worden ingeschakeld**.

Selecteer de aanbeveling of open de pagina adaptieve toepassings besturings elementen om de lijst met voorgestelde, bekende veilige toepassingen en groepen machines weer te geven.

1. Open het dash board van Azure Defender en selecteer in het gebied geavanceerde beveiliging de optie **adaptieve toepassings besturings elementen**.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Besturings elementen voor adaptieve toepassingen openen vanuit het Azure-dash board" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    De pagina **adaptieve toepassings besturings elementen** wordt geopend met de vm's die zijn gegroepeerd op de volgende tabbladen:

    - **Geconfigureerd** : groepen computers die al een gedefinieerde lijst met toegestane apps hebben. Het tabblad geconfigureerd wordt voor elke groep weer gegeven:
        - het aantal machines in de groep
        - recente waarschuwingen

    - **Aanbevolen** : groepen computers waarop dezelfde toepassingen consistent worden uitgevoerd en waarvoor geen acceptatie lijst is geconfigureerd. We raden u aan om besturings elementen voor adaptieve toepassingen voor deze groepen in te scha kelen.
    
      > [!TIP]
      > Als u een groeps naam met het voor voegsel ' REVIEWGROUP ' ziet, bevat deze machines met een gedeeltelijk consistente lijst met toepassingen. Security Center geen patroon ziet, maar u wordt aangeraden deze groep te controleren om te zien of _u_ hand matig een aantal regels voor adaptieve toepassings besturings elementen kunt definiëren zoals beschreven in [de regel voor het aanpassen van een groep met adaptieve toepassings besturings elementen bewerken](#edit-a-groups-adaptive-application-controls-rule).
      >
      > U kunt computers ook van deze groep naar andere groepen verplaatsen, zoals beschreven in [een machine verplaatsen van de ene groep naar een andere](#move-a-machine-from-one-group-to-another).

    - **Geen aanbeveling** : machines zonder een gedefinieerde lijst met toegestane toepassingen en die de functie niet ondersteunen. Uw computer kan om de volgende redenen op dit tabblad staan:
      - Er ontbreekt een Log Analytics agent
      - De Log Analytics-agent verzendt geen gebeurtenissen
      - Het is een Windows-computer met een reeds bestaand [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) -beleid ingeschakeld door ofwel een GPO ofwel een lokaal beveiligings beleid

      > [!TIP]
      > Security Center moet ten minste twee weken aan gegevens hebben om de unieke aanbevelingen per groep machines te definiëren. Computers die onlangs zijn gemaakt of die deel uitmaken van abonnementen die alleen recent zijn ingeschakeld met Azure Defender, worden weer gegeven op het tabblad **geen aanbeveling** .


1. Open het tabblad **Aanbevolen** . De groepen computers met aanbevolen acceptatie lijsten worden weer gegeven.

   ![Tabblad Aanbevolen](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Selecteer een groep. 

1. Als u de nieuwe regel wilt configureren, bekijkt u de verschillende secties van de pagina **regels voor toepassings beheer configureren** en de inhoud die uniek is voor deze specifieke groep computers:

   ![Een nieuwe regel configureren](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Machines selecteren** : standaard worden alle computers in de geïdentificeerde groep geselecteerd. Selecteer een optie om deze uit deze regel te verwijderen.
   
   1. **Aanbevolen toepassingen** : Bekijk deze lijst met toepassingen die gemeen schappelijk zijn voor de computers in deze groep en aanbevolen zijn om uit te voeren.
   
   1. **Meer toepassingen** : Bekijk deze lijst met toepassingen die minder vaak worden gezien op de computers in deze groep of waarvan bekend is dat deze kunnen worden gebruikt. Een waarschuwings pictogram geeft aan dat een specifieke toepassing door een aanvaller kan worden gebruikt om een lijst met toegestane toepassingen te omzeilen. U wordt aangeraden deze toepassingen zorgvuldig te bekijken.

      > [!TIP]
      > Beide toepassings lijsten bevatten de optie om een bepaalde toepassing te beperken tot bepaalde gebruikers. Het principe van minimale bevoegdheden waar mogelijk wordt aangenomen.
      > 
      > Toepassingen worden gedefinieerd door hun uitgevers, als een toepassing geen uitgever gegevens heeft (deze is niet ondertekend), wordt er een padregel gemaakt voor het volledige pad van de specifieke toepassing.

   1. Als u de regel wilt Toep assen, selecteert u **controle**. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>De regel voor de besturings elementen voor adaptieve toepassingen van een groep bewerken

U kunt ervoor kiezen om de acceptatie lijst voor een groep machines te bewerken vanwege bekende wijzigingen in uw organisatie. 

De regels voor een groep machines bewerken:

1. Open het dash board van Azure Defender en selecteer in het gebied geavanceerde beveiliging de optie **adaptieve toepassings besturings elementen**.

1. Selecteer op het tabblad **geconfigureerd** de groep met de regel die u wilt bewerken.

1. Bekijk de verschillende secties van de pagina **regels voor toepassings beheer configureren** zoals beschreven in [besturings elementen voor adaptieve toepassingen inschakelen op een groep machines](#enable-application-controls-on-a-group-of-machines).

1. Voeg eventueel een of meer aangepaste regels toe:

   1. Selecteer **regel toevoegen**.

      ![Een aangepaste regel toevoegen](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Als u een bekend veilig pad definieert, wijzigt u het **regel type** in ' pad ' en voert u één pad in. U kunt joker tekens in het pad toevoegen.
   
      > [!TIP]
      > Enkele scenario's waarvoor joker tekens in een pad handig kunnen zijn:
      > 
      > * Een Joker teken aan het einde van een pad gebruiken om alle uitvoer bare bestanden in deze map en submappen toe te staan.
      > * Een Joker teken gebruiken in het midden van een pad om een bekende naam van een uitvoerbaar bestand met een gewijzigde mapnaam in te scha kelen (bijvoorbeeld persoonlijke gebruikers mappen met een bekend uitvoerbaar bestand, automatisch gegenereerde mapnamen, enzovoort).
  
   1. Definieer de toegestane gebruikers en typen beveiligde bestanden.

   1. Wanneer u klaar bent met het definiëren van de regel, selecteert u **toevoegen**.

1. Selecteer **Opslaan** om de wijzigingen toe te passen.


## <a name="review-and-edit-a-groups-settings"></a>De instellingen van een groep controleren en bewerken

1. Als u de details en instellingen van uw groep wilt weer geven, selecteert u **groeps instellingen**

    Dit deel venster toont de naam van de groep (die kan worden gewijzigd), het type besturings systeem, de locatie en andere relevante informatie.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="De pagina groeps instellingen voor besturings elementen voor adaptieve toepassingen" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. U kunt desgewenst de naam van de groep of de beveiligings modus voor het bestands type wijzigen.

1. Selecteer **Toep assen** en **Opslaan**.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Reageren op ' Allowlist-regels in uw adaptief toepassings beheer beleid moeten worden bijgewerkt ' aanbeveling

U ziet deze aanbeveling wanneer de machine learning van Security Center mogelijk legitiem gedrag identificeert dat niet eerder is toegestaan. De aanbeveling stelt nieuwe regels voor uw bestaande definities voor om het aantal foutieve positieve waarschuwingen te verminderen.

De problemen oplossen:

1. Selecteer op de pagina aanbevelingen de **Allowlist-regels in het adaptieve toepassings beheer beleid moet worden bijgewerkt** om groepen met een nieuw geïdentificeerd, mogelijk legitiem gedrag te zien.

1. Selecteer de groep met de regel die u wilt bewerken.

1. Bekijk de verschillende secties van de pagina **regels voor toepassings beheer configureren** zoals beschreven in [besturings elementen voor adaptieve toepassingen inschakelen op een groep machines](#enable-application-controls-on-a-group-of-machines).

1. Selecteer **audit** om de wijzigingen toe te passen.




## <a name="audit-alerts-and-violations"></a>Waarschuwingen en schendingen controleren

1. Open het dash board van Azure Defender en selecteer in het gebied geavanceerde beveiliging de optie **adaptieve toepassings besturings elementen**.

1. Als u groepen wilt weer geven met machines met recente waarschuwingen, controleert u de groepen die worden vermeld op het tabblad **geconfigureerd** .

1. Selecteer een groep om verder te onderzoeken.

   ![Recente waarschuwingen](./media/security-center-adaptive-application/recent-alerts.png)

1. Selecteer een waarschuwing voor meer informatie en de lijst met betrokken computers.

    De pagina waarschuwingen toont meer informatie over de waarschuwingen en bevat een **actie** koppeling met aanbevelingen voor het oplossen van de dreiging.

    :::image type="content" source="media/security-center-adaptive-application/adaptive-application-alerts-start-time.png" alt-text="De begin tijd van de waarschuwingen voor besturings elementen voor adaptieve toepassingen is de ":::

    > [!NOTE]
    > Met besturings elementen voor adaptieve toepassingen worden gebeurtenissen elke twaalf uur berekend. De ' begin tijd van de activiteit ' die wordt weer gegeven op de pagina waarschuwingen is de tijd dat de waarschuwing wordt gemaakt door besturings elementen voor adaptieve toepassingen, **niet** op het moment dat het verdachte proces actief was.


## <a name="move-a-machine-from-one-group-to-another"></a>Een machine van de ene naar de andere groep verplaatsen

Wanneer u een machine van de ene naar de andere groep verplaatst, wordt het toepassings beheer beleid dat wordt toegepast, gewijzigd in de instellingen van de groep waarnaar u het hebt verplaatst. U kunt een machine ook van een geconfigureerde groep naar een niet-geconfigureerde groep verplaatsen, waardoor alle toepassings beheer regels die op de machine zijn toegepast, worden verwijderd.

1. Open het dash board van Azure Defender en selecteer in het gebied geavanceerde beveiliging de optie **adaptieve toepassings besturings elementen**.

1. Selecteer op de pagina **adaptieve toepassings besturings elementen** op het tabblad **geconfigureerd** de groep die de computer bevat die moet worden verplaatst.

1. Open de lijst met  **geconfigureerde machines**.

1. Open het menu van de computer vanaf drie punten aan het einde van de rij en selecteer **verplaatsen**. De **computer verplaatsen naar een ander groeps** deel venster wordt geopend.

1. Selecteer de doel groep en selecteer **machine verplaatsen**.

1. Selecteer **Opslaan** om uw wijzigingen op te slaan.





## <a name="manage-application-controls-via-the-rest-api"></a>Besturings elementen voor toepassingen beheren via de REST API 

Als u de besturings elementen voor adaptieve toepassingen programmatisch wilt beheren, gebruikt u onze REST API. 

De relevante API-documentatie is beschikbaar in [het gedeelte met besturings elementen voor adaptieve toepassingen van de API-documenten van Security Center](/rest/api/securitycenter/adaptiveapplicationcontrols).

Enkele van de functies die beschikbaar zijn via de REST API:

* De **lijst** haalt alle groeps aanbevelingen op en biedt een JSON met een object voor elke groep.

* **Haal** de JSON op met de volledige aanbevelings gegevens (dat wil zeggen, een lijst met computers, regels voor Uitgever/pad, enzovoort).

* Met **put** configureert u uw regel (gebruik de JSON die u hebt opgehaald met **Get** als de hoofd tekst voor deze aanvraag).
 
   > [!IMPORTANT]
   > De **put** -functie verwacht minder para meters dan de JSON die wordt geretourneerd door de opdracht Get contains.
   >
   > Verwijder de volgende eigenschappen voordat u de JSON gebruikt in de put-aanvraag: recommendationStatus, configurationStatus, issues, location en hebben.


## <a name="faq---adaptive-application-controls"></a>Veelgestelde vragen-besturings elementen voor adaptieve toepassingen

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Zijn er opties voor het afdwingen van de besturings elementen van de toepassing?
Er zijn momenteel geen afdwingings opties beschikbaar. Besturings elementen voor adaptieve toepassingen zijn bedoeld om **beveiligings waarschuwingen** te bieden als een andere toepassing dan de toepassingen die u hebt gedefinieerd als veilig worden uitgevoerd. Ze hebben diverse voor delen ([Wat zijn de voor delen van adaptieve toepassings besturings elementen?](#what-are-the-benefits-of-adaptive-application-controls)) en zijn zeer aanpasbaar, zoals op deze pagina wordt weer gegeven.

 

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u adaptief toepassings beheer in Azure Security Center kunt gebruiken om lijst toestaan te definiëren van toepassingen die worden uitgevoerd op uw Azure-en niet-Azure-machines. Zie voor meer informatie over enkele van de andere functies van de Cloud beveiliging van een andere Security Center.

* [Meer informatie over just-in-time-toegang (JIT) voor VM's](just-in-time-explained.md)
* [Uw Azure Kubernetes-clusters beveiligen](defender-for-kubernetes-introduction.md)