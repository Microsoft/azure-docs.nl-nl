---
title: Adaptieve toepassingsbesturingselementen in Azure Security Center
description: Dit document helpt u bij het gebruik van adaptief toepassingsbeheer in Azure Security Center om lijsttoepassingen toe te staan die worden uitgevoerd op Azure-machines.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 9e2dcace673a1c7215634434f9e89ddc6b953a63
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834605"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Adaptieve toepassingsregelaars gebruiken om de aanvalsoppervlakken van uw computers te verminderen

Meer informatie over de voordelen Azure Security Center adaptieve toepassingsregelaars van uw bedrijf en hoe u uw beveiliging kunt verbeteren met deze gegevensgestuurde, intelligente functie.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Wat zijn Security Center adaptieve toepassingsregelaars van de Security Center?

Adaptieve toepassingsregelaars zijn een intelligente en geautomatiseerde oplossing voor het definiëren van toegestane lijsten met bekende veilige toepassingen voor uw computers. 

Organisaties hebben vaak verzamelingen machines die regelmatig dezelfde processen uitvoeren. Security Center gebruikt machine learning om de toepassingen te analyseren die op uw computers worden uitgevoerd en om een lijst met de bekende veilige software te maken. Lijsten met toegestane lijsten zijn gebaseerd op uw specifieke Azure-workloads en u kunt de aanbevelingen verder aanpassen met behulp van de onderstaande instructies.

Wanneer u adaptieve toepassingsregelaars hebt ingeschakeld en geconfigureerd, krijgt u beveiligingswaarschuwingen als er andere toepassingen worden uitgevoerd dan degene die u als veilig hebt gedefinieerd.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Wat zijn de voordelen van adaptieve toepassingsregelaars?

Door lijsten met bekende veilige toepassingen te definiëren en waarschuwingen te genereren wanneer iets anders wordt uitgevoerd, kunt u meerdere harde doelen bereiken:

- Mogelijke malware identificeren, zelfs alle malware die mogelijk wordt gemist door antimalwareoplossingen
- Naleving van lokaal beveiligingsbeleid verbeteren dat het gebruik van alleen gelicentieerde software bepaalt
- Voorkomen dat oude of niet-ondersteunde toepassingen worden uitgevoerd
- Specifieke software voorkomen die is verboden door uw organisatie
- Meer toezicht op apps die toegang hebben tot gevoelige gegevens

Er zijn momenteel geen afdwingingsopties beschikbaar. Adaptieve toepassingsregelaars zijn bedoeld om beveiligingswaarschuwingen te bieden als een toepassing wordt uitgevoerd anders dan de toepassingen die u als veilig hebt gedefinieerd.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene beschikbaarheid (GA)|
|Prijzen:|[Azure Defender voor servers](defender-for-servers-introduction.md) is vereist|
|Ondersteunde machines:|![Ja, ](./media/icons/yes-icon.png) Azure- en niet-Azure-machines met Windows en Linux<br>![Ja, ](./media/icons/yes-icon.png) [Azure Arc](../azure-arc/index.yml) machines|
|Vereiste rollen en machtigingen:|**De rollen Beveiligingslezer** **en** Lezer kunnen zowel groepen als de lijsten met bekende veilige toepassingen weergeven<br>**De** rollen **Inzender en Beveiligingsbeheerder** kunnen zowel groepen als de lijsten met bekende veilige toepassingen bewerken|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (Overheid van de VS, China, andere overheden)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Toepassingsbesturingselementen op een groep machines inschakelen

Als Security Center groepen computers in uw abonnementen heeft geïdentificeerd die consistent een vergelijkbare set toepassingen uitvoeren, wordt u gevraagd de volgende aanbeveling uit te voeren: Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw **computers.**

Selecteer de aanbeveling of open de pagina adaptieve toepassingsregelaars om de lijst met voorgestelde, bekende veilige toepassingen en groepen machines weer te geven.

1. Open het Azure Defender dashboard en selecteer adaptieve toepassingsbesturingselementen in het gebied **voor geavanceerde beveiliging.**

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Adaptieve toepassingsbesturingselementen openen vanuit het Azure-dashboard" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    De **pagina Adaptieve toepassingsbesturingselementen** wordt geopend met uw VM's gegroepeerd op de volgende tabbladen:

    - **Geconfigureerd:** groepen machines die al een gedefinieerde lijst met toegestane toepassingen hebben. Voor elke groep wordt op het geconfigureerde tabblad het volgende weergegeven:
        - het aantal machines in de groep
        - recente waarschuwingen

    - **Aanbevolen:** groepen computers die consistent dezelfde toepassingen uitvoeren en geen lijst met toegestane toepassingen hebben geconfigureerd. U wordt aangeraden adaptieve toepassingsregelaars voor deze groepen in teschakelen.
    
      > [!TIP]
      > Als u een groepsnaam met het voorvoegsel REVIEWGROUP ziet, bevat deze machines met een gedeeltelijk consistente lijst met toepassingen. Security Center kan geen patroon zien, maar raadt aan deze  groep te controleren om te zien of u handmatig bepaalde regels voor adaptieve toepassingsbesturingselementen kunt definiëren, zoals beschreven in De regel voor adaptieve toepassingsregelaars van een groep [bewerken.](#edit-a-groups-adaptive-application-controls-rule)
      >
      > U kunt ook machines van deze groep naar andere groepen verplaatsen, zoals beschreven in [Een machine van de ene naar](#move-a-machine-from-one-group-to-another)de andere groep verplaatsen.

    - **Geen aanbeveling:** machines zonder een gedefinieerde lijst met toegestane toepassingen en die de functie niet ondersteunen. Uw computer staat mogelijk op dit tabblad om de volgende redenen:
      - Er ontbreekt een Log Analytics-agent
      - De Log Analytics-agent verstuurt geen gebeurtenissen
      - Het is een Windows-computer met een bestaand [AppLocker-beleid](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) dat is ingeschakeld door een GPO of een lokaal beveiligingsbeleid

      > [!TIP]
      > Security Center heeft ten minste twee weken aan gegevens nodig om de unieke aanbevelingen per groep computers te definiëren. Machines die onlangs zijn gemaakt of die deel uitmaken van abonnementen die pas onlangs zijn ingeschakeld met Azure Defender, worden weergegeven op **het** tabblad Geen aanbeveling.


1. Open het **tabblad** Aanbevolen. De groepen computers met aanbevolen lijsten met toegestane worden weergegeven.

   ![Aanbevolen tabblad](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Selecteer een groep. 

1. Als u de nieuwe regel wilt configureren, bekijkt u de verschillende secties van deze pagina Regels voor toepassingsbeheer configureren en de inhoud die uniek is voor deze specifieke groep computers: 

   ![Een nieuwe regel configureren](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Machines selecteren:** standaard worden alle machines in de geïdentificeerde groep geselecteerd. Schakel de selectie uit om ze uit deze regel te verwijderen.
   
   1. **Aanbevolen toepassingen:** bekijk deze lijst met toepassingen die gemeenschappelijk zijn voor de computers in deze groep en aanbevolen om uit te voeren.
   
   1. **Meer toepassingen:** bekijk deze lijst met toepassingen die minder vaak worden weergegeven op de computers in deze groep of die bekend staan als exploiteerbaar. Een waarschuwingspictogram geeft aan dat een specifieke toepassing kan worden gebruikt door een aanvaller om een lijst met toegestane toepassingen te omzeilen. U wordt aangeraden deze toepassingen zorgvuldig te controleren.

      > [!TIP]
      > Beide toepassingslijsten bevatten de optie om een specifieke toepassing te beperken tot bepaalde gebruikers. Gebruik waar mogelijk het principe van minste bevoegdheden.
      > 
      > Toepassingen worden gedefinieerd door hun uitgevers. Als een toepassing geen uitgeversinformatie heeft (deze is niet ondertekend), wordt er een padregel gemaakt voor het volledige pad van de specifieke toepassing.

   1. Selecteer Controleren om de regel toe **te passen.** 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>De regel voor adaptieve toepassingsregelaars van een groep bewerken

U kunt besluiten om de lijst met toegestane machines te bewerken vanwege bekende wijzigingen in uw organisatie. 

De regels voor een groep machines bewerken:

1. Open het Azure Defender dashboard en selecteer adaptieve toepassingsbesturingselementen in het gebied **voor geavanceerde beveiliging.**

1. Selecteer op **het tabblad** Geconfigureerd de groep met de regel die u wilt bewerken.

1. Bekijk de verschillende secties van de pagina Regels voor toepassingsbeheer **configureren,** zoals beschreven in Adaptieve toepassingsregelaars [inschakelen op een groep computers.](#enable-application-controls-on-a-group-of-machines)

1. Voeg desgewenst een of meer aangepaste regels toe:

   1. Selecteer **Regel toevoegen.**

      ![Een aangepaste regel toevoegen](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Als u een bekend veilig pad definit, wijzigt u het **regeltype** in Pad en voert u één pad in. U kunt jokertekens opnemen in het pad.
   
      > [!TIP]
      > Sommige scenario's waarvoor jokertekens in een pad nuttig kunnen zijn:
      > 
      > * Gebruik een jokerteken aan het einde van een pad om alle uitvoerbare bestanden in deze map en submappen toe te staan.
      > * Gebruik een jokerteken in het midden van een pad om een bekende naam van het uitvoerbare bestand in te stellen met een naam die de map verandert (bijvoorbeeld persoonlijke gebruikersmappen met een bekend uitvoerbaar bestand, automatisch gegenereerde mapnamen, enzovoort).
  
   1. Definieer de toegestane gebruikers en beveiligde bestandstypen.

   1. Wanneer u klaar bent met het definiëren van de regel, selecteert u **Toevoegen.**

1. Selecteer Opslaan om de wijzigingen toe **te passen.**


## <a name="review-and-edit-a-groups-settings"></a>De instellingen van een groep controleren en bewerken

1. Als u de details en instellingen van uw groep wilt weergeven, selecteert u **Groepsinstellingen**

    In dit deelvenster ziet u de naam van de groep (die kan worden gewijzigd), het type besturingssysteem, de locatie en andere relevante details.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="De pagina met groepsinstellingen voor adaptieve toepassingsbesturingselementen" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Wijzig eventueel de beveiligingsmodi voor de naam van de groep of het bestandstype.

1. Selecteer **Toepassen** en **Opslaan.**



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Reageren op de aanbeveling 'Allowlist rules in your adaptive application control policy should be updated' (Allowlist-regels in uw adaptieve toepassingsbeheerbeleid moeten worden bijgewerkt)

U ziet deze aanbeveling wanneer Security Center de machine learning mogelijk legitiem gedrag identificeert dat niet eerder is toegestaan. De aanbeveling stelt nieuwe regels voor uw bestaande definities voor om het aantal fout-positieve waarschuwingen te verminderen.

De problemen oplossen:

1. Selecteer op de pagina aanbevelingen de aanbeveling **Allowlist rules in your adaptive application control** policy should be updated recommendation (Allowlist-regels in uw adaptieve toepassingsbeheerbeleid moeten worden bijgewerkt) om groepen te zien met nieuw geïdentificeerd, mogelijk legitiem gedrag.

1. Selecteer de groep met de regel die u wilt bewerken.

1. Bekijk de verschillende secties van de pagina Regels voor toepassingsbeheer **configureren,** zoals beschreven in Adaptieve toepassingsregelaars [inschakelen op een groep computers.](#enable-application-controls-on-a-group-of-machines)

1. Selecteer Controleren om de wijzigingen toe **te passen.**




## <a name="audit-alerts-and-violations"></a>Waarschuwingen en schendingen controleren

1. Open het Azure Defender dashboard en selecteer adaptieve toepassingsbesturingselementen in het gebied voor **geavanceerde beveiliging.**

1. Als u groepen wilt zien met machines die recente waarschuwingen hebben, bekijkt u de groepen die worden vermeld op **het tabblad Geconfigureerd.**

1. Als u verder wilt onderzoeken, selecteert u een groep.

   ![Recente waarschuwingen](./media/security-center-adaptive-application/recent-alerts.png)

1. Selecteer een waarschuwing voor meer informatie en de lijst met betrokken machines.

    De waarschuwingspagina bevat meer informatie over de waarschuwingen en een koppeling Actie **ondernemen** met aanbevelingen voor het beperken van de bedreiging.

    :::image type="content" source="media/security-center-adaptive-application/adaptive-application-alerts-start-time.png" alt-text="De begintijd van waarschuwingen voor adaptieve toepassingsregelaars is de ":::

    > [!NOTE]
    > Adaptieve toepassingsregelaars berekenen gebeurtenissen eenmaal per twaalf uur. De begintijd van de activiteit die wordt weergegeven op de pagina Waarschuwingen is de tijd dat adaptieve toepassingsregelaars de waarschuwing hebben **gemaakt,** niet het tijdstip dat het verdachte proces actief was.


## <a name="move-a-machine-from-one-group-to-another"></a>Een machine van de ene groep naar de andere verplaatsen

Wanneer u een machine van de ene groep naar de andere verplaatst, wordt het toegepaste toepassingsbeheerbeleid gewijzigd in de instellingen van de groep waar u deze naar hebt verplaatst. U kunt een machine ook verplaatsen van een geconfigureerde groep naar een niet-geconfigureerde groep. Hierdoor worden alle regels voor toepassingsbeheer verwijderd die op de machine zijn toegepast.

1. Open het Azure Defender dashboard en selecteer adaptieve toepassingsbesturingselementen in het gebied **voor geavanceerde beveiliging.**

1. Selecteer op **de pagina Adaptieve** toepassingsregelaars **op het tabblad** Geconfigureerd de groep met de machine die moet worden verplaatst.

1. Open de lijst met **geconfigureerde machines.**

1. Open het menu van de machine vanaf drie punten aan het einde van de rij en selecteer **Verplaatsen.** Het **deelvenster Machine verplaatsen naar een andere groep** wordt geopend.

1. Selecteer de doelgroep en selecteer **Machine verplaatsen.**

1. Selecteer **Opslaan** om uw wijzigingen op te slaan.





## <a name="manage-application-controls-via-the-rest-api"></a>Toepassingsbesturingselementen beheren via de REST API 

Als u uw adaptieve toepassingsregelaars programmatisch wilt beheren, gebruikt u onze REST API. 

De relevante API-documentatie is beschikbaar in [de sectie Adaptieve toepassingsregelaars van Security Center API-documenten van .](/rest/api/securitycenter/adaptiveapplicationcontrols)

Enkele van de functies die beschikbaar zijn via de REST API:

* **List** haalt al uw groepsaanbevelingen op en biedt een JSON met een -object voor elke groep.

* **Get** haalt de JSON op met de volledige aanbevelingsgegevens (dat wil zeggen, lijst met machines, regels voor uitgever/pad, etc.).

* **Met Put** configureert u de regel (gebruik de JSON die u hebt opgehaald **met Ophalen** als hoofdregel voor deze aanvraag).
 
   > [!IMPORTANT]
   > De **functie Put** verwacht minder parameters dan de JSON die wordt geretourneerd door de opdracht Get.
   >
   > Verwijder de volgende eigenschappen voordat u de JSON in de Put-aanvraag gebruikt: recommendationStatus, configurationStatus, issues, location en sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Veelgestelde vragen - Adaptieve toepassingsbesturingselementen

- [Zijn er opties om de toepassingsbesturingselementen af te dwingen?](#are-there-any-options-to-enforce-the-application-controls)
- [Waarom zie ik een Qualys-app in mijn aanbevolen toepassingen?](#why-do-i-see-a-qualys-app-in-my-recommendeded-applications)

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Zijn er opties om de toepassingsbesturingselementen af te dwingen?
Er zijn momenteel geen afdwingingsopties beschikbaar. Adaptieve toepassingsregelaars zijn bedoeld om **beveiligingswaarschuwingen** te geven als een toepassing wordt uitgevoerd die niet de toepassingen is die u als veilig hebt gedefinieerd. Ze hebben diverse voordelen ( Wat zijn de voordelen van[adaptieve toepassingsregelaars?](#what-are-the-benefits-of-adaptive-application-controls)) en zijn zeer aanpasbaar, zoals wordt weergegeven op deze pagina.

### <a name="why-do-i-see-a-qualys-app-in-my-recommendeded-applications"></a>Waarom zie ik een Qualys-app in mijn aanbevolen toepassingen?
[Azure Defender voor servers omvat](defender-for-servers-introduction.md) het scannen van beveiligingsleed voor uw computers zonder extra kosten. U hebt geen Qualys-licentie of Qualys-account nodig. De scans worden naadloos uitgevoerd in Security Center. Zie De geïntegreerde oplossing voor evaluatie van beveiligingsledigheden van Defender voor meer informatie over deze scanner en instructies voor het [implementeren ervan.](deploy-vulnerability-assessment-vm.md)

Om ervoor te zorgen dat er geen waarschuwingen worden gegenereerd wanneer Security Center scanner implementeert, bevat de lijst met aanbevolen toegestane toepassingsregelaars de scanner voor alle computers. 


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u adaptief toepassingsbeheer in Azure Security Center om lijsten met toegestane toepassingen te definiëren die worden uitgevoerd op uw Azure- en niet-Azure-machines. Zie voor meer informatie over een aantal Security Center van de andere functies voor de beveiliging van cloudworkloads:

* [Meer informatie over just-in-time-toegang (JIT) voor VM's](just-in-time-explained.md)
* [Uw Azure Kubernetes-clusters beveiligen](defender-for-kubernetes-introduction.md)