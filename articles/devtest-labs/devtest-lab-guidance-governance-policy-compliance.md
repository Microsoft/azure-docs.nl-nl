---
title: Bedrijfs beleid en-naleving in Azure DevTest Labs
description: Dit artikel bevat richt lijnen voor het beheren van het bedrijfs beleid en de naleving van de Azure DevTest Labs-infra structuur.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 89fe380c3a8e0e0a82cc806bf19f9a3454041d94
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592344"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Governance van Azure DevTest Labs-infra structuur-bedrijfs beleid en-naleving
Dit artikel bevat richt lijnen voor het beheren van het bedrijfs beleid en de naleving van de Azure DevTest Labs-infra structuur. 

## <a name="public-vs-private-artifact-repository"></a>Openbaar versus persoonlijke artefact opslagplaats

### <a name="question"></a>Vraag
Wanneer moet een organisatie een open bare artefact opslagplaats versus een persoonlijke artefact opslagplaats in DevTest Labs gebruiken?

### <a name="answer"></a>Antwoord
De [opslag plaats voor open bare artefacten](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) bevat een initiële set software pakketten die het meest worden gebruikt. Het helpt bij snelle implementatie zonder dat u tijd hoeft te investeren om algemene ontwikkel hulpprogramma's en-invoeg toepassingen te reproduceren. U kunt ervoor kiezen om hun eigen privé-opslag plaats te implementeren. U kunt een open bare en een privé opslag plaats parallel gebruiken. U kunt er ook voor kiezen om de open bare opslag plaats uit te scha kelen. De criteria voor het implementeren van een privé-opslag plaats moeten worden aangestuurd door de volgende vragen en overwegingen:

- Heeft de organisatie een vereiste om software met een bedrijfs licentie te hebben als onderdeel van hun DevTest Labs-aanbieding? Als het antwoord ja is, moet er een privé opslagplaats worden gemaakt.
- Ontwikkelt de organisatie aangepaste software die een specifieke bewerking biedt, die vereist is als onderdeel van het algemene inrichtings proces? Als het antwoord ja is, moet een privé opslagplaats worden geïmplementeerd.
- Als het governance beleid van de organisatie isolatie vereist en externe opslag plaatsen niet onder het directe configuratie beheer van de organisatie zijn, moet een opslag plaats voor persoonlijke artefacten worden geïmplementeerd. Als onderdeel van dit proces kan een eerste kopie van de open bare opslag plaats worden gekopieerd en geïntegreerd met de privé-opslag plaats. Vervolgens kan de open bare opslag plaats worden uitgeschakeld, zodat niemand binnen de organisatie er meer toegang toe heeft. Deze aanpak zorgt ervoor dat alle gebruikers binnen de organisatie slechts één opslag plaats hebben die door de organisatie is goedgekeurd en de configuratie drift minimaliseert.

### <a name="single-repository-or-multiple-repositories"></a>Eén opslag plaats of meerdere opslag plaatsen 

### <a name="question"></a>Vraag
Moet een organisatie plannen voor één opslag plaats of meerdere opslag plaatsen toestaan?

### <a name="answer"></a>Antwoord
Als onderdeel van de algehele beheer-en configuratie strategie van uw organisatie wordt u aangeraden een gecentraliseerde opslag plaats te gebruiken. Wanneer u meerdere opslag plaatsen gebruikt, kunnen ze gedurende de periode een silo van onbeheerde software worden. Met een centrale opslag plaats kunnen meerdere teams artefacten uit deze opslag plaats gebruiken voor hun projecten. Het dwingt de standaardisatie af, de beveiliging, het gemak van het beheer en elimineert het dupliceren van inspanningen. Als onderdeel van de gecentraliseerde zijn de volgende acties aanbevolen procedures voor het beheer en duurzaamheid op lange termijn:

- Koppel de Azure-opslag plaatsen aan dezelfde Azure Active Directory-Tenant die door het Azure-abonnement wordt gebruikt voor verificatie en autorisatie.
- Maak een groep met **de naam alle ontwikkel aars van DevTest Labs** in azure Active Directory die centraal worden beheerd. Ontwikkel aars die bijdraagt aan het ontwikkelen van artefacten, moeten in deze groep worden geplaatst.
- Dezelfde Azure Active Directory groep kan worden gebruikt om toegang te bieden tot de Azure opslag plaatsen-opslag plaats en het lab.
- In azure opslag plaatsen moet vertakkingen of forking worden gebruikt voor het scheiden van een opslag plaats in ontwikkeling van de primaire productie opslagplaats. Inhoud wordt alleen toegevoegd aan de hoofd vertakking met een pull-aanvraag nadat de juiste code is gecontroleerd. Zodra de code revisor de wijziging heeft goedgekeurd, wordt een lead ontwikkelaar, die verantwoordelijk is voor het onderhoud van de hoofd vertakking, samengevoegd met de bijgewerkte code. 

## <a name="corporate-security-policies"></a>Beveiligings beleid van het bedrijf

### <a name="question"></a>Vraag
Hoe kan een organisatie ervoor zorgen dat beveiligings beleid voor ondernemingen wordt toegepast?

### <a name="answer"></a>Antwoord
Een organisatie kan dit doen door de volgende acties uit te voeren:

1. Het ontwikkelen en publiceren van een uitgebreid beveiligings beleid. Het beleid heeft betrekking op de regels van acceptabel gebruik die zijn gekoppeld aan het gebruik van software, Cloud activa. Ook wordt gedefinieerd wat het beleid duidelijk schendt. 
2. Ontwikkel een aangepaste installatie kopie, aangepaste artefacten en een implementatie proces dat indeling toestaat binnen de beveiligings-realm die is gedefinieerd met Active Directory. Deze benadering dwingt de bedrijfs grens af en stelt een gemeen schappelijke set omgevings controles in. Deze besturings elementen voor de omgeving van een ontwikkelaar kunnen overwegen bij het ontwikkelen en volgen van een beveiligde ontwikkelings levenscyclus als onderdeel van hun algemene proces. Het doel is ook om een omgeving te bieden die niet meer beperkend is en die de ontwikkeling kan belemmeren, maar een redelijke set besturings elementen. De groeps beleidsregels in de organisatie-eenheid (OE) die virtuele machines van het Lab bevat, kunnen een subset zijn van het totale groeps beleid dat in productie is gevonden. Het is ook mogelijk dat ze een extra set zijn om geïdentificeerde Risico's goed te kunnen beperken.

## <a name="data-integrity"></a>Gegevensintegriteit

### <a name="question"></a>Vraag
Hoe kan een organisatie gegevens integriteit garanderen om ervoor te zorgen dat externe ontwikkel aars de code niet kunnen verwijderen of malware of niet-goedgekeurde software introduceren?

### <a name="answer"></a>Antwoord
Er zijn verschillende lagen waarmee u de dreiging kunt beperken van externe consultants, aannemers of werk nemers die extern zijn in om samen te werken in DevTest Labs. 

Zoals eerder vermeld, moet de eerste stap een acceptabel beleid voor gebruik hebben opgesteld en gedefinieerd, waardoor duidelijk de gevolgen worden aangegeven wanneer iemand het beleid schendt. 

De eerste laag van besturings elementen voor externe toegang is het Toep assen van een beleid voor externe toegang via een VPN-verbinding die niet rechtstreeks is verbonden met het lab. 

De tweede laag van besturings elementen is het Toep assen van een set groeps beleidsobjecten die het kopiëren en plakken via extern bureau blad verhinderen. Een netwerk beleid kan worden geïmplementeerd om uitgaande services van de omgeving, zoals FTP-en RDP-Services, niet uit de omgeving toe te staan. Door de gebruiker gedefinieerde route ring kan alle Azure-netwerk verkeer terugbrengen naar on-premises, maar de route ring kan niet worden ingesteld voor alle Url's die het uploaden van gegevens kunnen toestaan, tenzij deze worden beheerd via een proxy om inhoud en sessies te scannen. Open bare Ip's kunnen worden beperkt binnen het virtuele netwerk dat DevTest Labs ondersteunt, zodat er geen externe netwerk bron kan worden overbrugd.

Uiteindelijk moet u hetzelfde type beperkingen Toep assen voor de hele organisatie, die ook moet gelden voor alle mogelijke methoden van Verwissel bare media of externe Url's die een bericht van inhoud kunnen accepteren. Neem contact op met uw Security Professional om een beveiligings beleid te controleren en te implementeren. Zie [micro soft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)(Engelstalig) voor meer aanbevelingen.


## <a name="next-steps"></a>Volgende stappen
Zie [toepassings migratie en-integratie](devtest-lab-guidance-governance-application-migration-integration.md).
