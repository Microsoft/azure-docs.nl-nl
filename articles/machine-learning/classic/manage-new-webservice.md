---
title: 'ML Studio (klassiek): webservices beheren-Azure'
description: Beheer uw Machine Learning nieuwe en klassieke webservices met behulp van de Microsoft Azure Machine Learning Web Services-portal. Aangezien klassieke webservices en nieuwe webservices zijn gebaseerd op verschillende onderliggende technologieën, hebt u voor elk van deze onderdelen enigszins verschillende beheer mogelijkheden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: d3aea23c816b9d8b32b0563d3116cdc2b92e080f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094305"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Een webservice beheren via de portal voor webservices van Azure Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


U kunt uw Machine Learning nieuwe en klassieke webservices beheren met behulp van de Microsoft Azure Machine Learning Web Services-portal. Aangezien klassieke webservices en nieuwe webservices zijn gebaseerd op verschillende onderliggende technologieën, hebt u voor elk van deze onderdelen enigszins verschillende beheer mogelijkheden.

In de Machine Learning Web Services-portal kunt u het volgende doen:

* Bewaak hoe de webservice wordt gebruikt.
* De beschrijving configureren, de sleutels voor de webservice bijwerken (nieuw alleen), de sleutel van uw opslag account bijwerken (nieuw alleen), logboek registratie inschakelen en voorbeeld gegevens in-of uitschakelen.
* Verwijder de webservice.
* Facturerings plannen maken, verwijderen of bijwerken (nieuw).
* Eind punten toevoegen en verwijderen (alleen klassiek)

>[!NOTE]
>U kunt klassieke webservices ook beheren in [machine learning Studio (klassiek)](https://studio.azureml.net) op het tabblad **webservices** .

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Machtigingen voor het beheren van nieuwe webservices op basis van Resource Manager

Nieuwe webservices worden geïmplementeerd als Azure-resources. U moet de juiste machtigingen hebben om nieuwe webservices te implementeren en te beheren.  Als u nieuwe webservices wilt implementeren of beheren, moet u een rol Inzender of beheerder toewijzen aan het abonnement waarop de webservice is geïmplementeerd. Als u een andere gebruiker uitnodigt voor een machine learning-werk ruimte, moet u deze toewijzen aan de rol Inzender of beheerder voor het abonnement voordat ze webservices kunnen implementeren of beheren. 

Als de gebruiker niet over de juiste machtigingen beschikt voor toegang tot resources in de Azure Machine Learning webservices-Portal, wordt de volgende fout weer gegeven bij het implementeren van een webservice:

*De webservice-implementatie is mislukt. Dit account heeft onvoldoende toegang tot het Azure-abonnement met de werk ruimte. Als u een webservice wilt implementeren in azure, moet hetzelfde account worden uitgenodigd voor de werk ruimte en moet het toegang krijgen tot het Azure-abonnement met de werk ruimte.*

Zie [een Azure machine learning Studio (klassieke) werk ruimte maken en delen](create-workspace.md)voor meer informatie over het maken van een werk ruimte.

Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van toegangs machtigingen.


## <a name="manage-new-web-services"></a>Nieuwe webservices beheren
Uw nieuwe webservices beheren:

1. Meld u aan bij de [Microsoft Azure machine learning Web Services](https://services.azureml.net/quickstart) -Portal met uw Microsoft Azure account: gebruik het account dat is gekoppeld aan het Azure-abonnement.
2. Klik in het menu op **Web Services**.

Hier wordt een lijst met geïmplementeerde webservices weer gegeven voor uw abonnement. 

Als u een webservice wilt beheren, klikt u op webservices. U kunt op de pagina Web Services het volgende doen:

* Klik op de webservice om deze te beheren.
* Klik op het facturerings abonnement voor de webservice om het bij te werken.
* Een webservice verwijderen.
* Kopieer een webservice en implementeer deze in een andere regio.

Wanneer u op een webservice klikt, wordt de pagina Quick Start van web service geopend. De Quick Start-pagina van web service heeft twee menu opties waarmee u uw webservice kunt beheren:

* **Dash board** : Hiermee kunt u het gebruik van webservices weer geven.
* **Configureren** : Hiermee kunt u beschrijvende tekst toevoegen, de sleutel bijwerken voor het opslag account dat is gekoppeld aan de webservice en voorbeeld gegevens in-of uitschakelen.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Controleren hoe de webservice wordt gebruikt
Klik op het tabblad **dash board** .

Vanuit het dash board kunt u het totale gebruik van uw web-service gedurende een bepaalde periode bekijken. U kunt de periode selecteren die u wilt weer geven in de vervolg keuzelijst periode in de rechter bovenhoek van de gebruiks diagrammen. Het dash board bevat de volgende informatie:

* **Aanvragen in** de loop van de tijd geeft een stap grafiek weer van het aantal aanvragen gedurende de geselecteerde tijds periode. Zo kunt u nagaan of u problemen ondervindt met pieken in het gebruik.
* **Aanvraag-antwoord aanvragen** hier wordt het totale aantal Request-Response-aanroepen weer gegeven dat de service heeft ontvangen over de geselecteerde tijds periode en hoeveel er is mislukt.
* **Gemiddelde Request-Response reken tijd** toont een gemiddelde van de tijd die nodig is om de ontvangen aanvragen uit te voeren.
* Met **batch-aanvragen** wordt het totale aantal batch-aanvragen weer gegeven dat de service heeft ontvangen over de geselecteerde tijds periode en hoeveel er is mislukt.
* Bij **gemiddelde taak latentie** wordt een gemiddelde weer gegeven van de tijd die nodig is om de ontvangen aanvragen uit te voeren.
* **Fouten** geeft het totale aantal fouten weer dat is opgetreden bij aanroepen naar de webservice.
* Met **kosten voor services** worden de kosten weer gegeven voor het facturerings plan dat is gekoppeld aan de service.

### <a name="configuring-the-web-service"></a>De webservice configureren
Klik op de menu optie **configureren** .

U kunt de volgende eigenschappen bijwerken:

* Met **Beschrijving** kunt u een beschrijving invoeren voor de webservice.
* Met **titel** kunt u een titel voor de webservice invoeren
* Met **sleutels** kunt u de primaire en secundaire API-sleutels draaien.
* Met de sleutel voor het **opslag account** kunt u de sleutel bijwerken voor het opslag account dat is gekoppeld aan de wijzigingen in de webservice. 
* Door **voorbeeld gegevens in te scha kelen** kunt u voorbeeld gegevens opgeven die u kunt gebruiken om de Request-Response-service te testen. Als u de webservice in Machine Learning Studio (klassiek) hebt gemaakt, worden de voorbeeld gegevens opgehaald uit de gegevens waarmee u uw model hebt getraind. Als u de service programmatisch hebt gemaakt, worden de gegevens opgehaald uit de voorbeeld gegevens die u hebt ingevoerd als onderdeel van het JSON-pakket.

### <a name="managing-billing-plans"></a>Facturerings plannen beheren
Klik op de menu optie **plannen** op de pagina Quick Start van Web Services. U kunt ook klikken op het plan dat is gekoppeld aan de specifieke webservice om dat plan te beheren.

* Met **Nieuw** kunt u een nieuw plan maken.
* Met een **plan exemplaar toevoegen/verwijderen** kunt u een bestaand plan uitschalen om capaciteit toe te voegen.
* Met **upgrade/downgrade** kunt u een bestaand plan omhoog schalen om capaciteit toe te voegen.
* Met **verwijderen** kunt u een abonnement verwijderen.

Klik op een plan om het dash board ervan weer te geven. Met het dash board kunt u een moment opname maken of het gebruik plannen voor een geselecteerde periode. Als u de tijds periode wilt selecteren die u wilt weer geven, klikt u op de vervolg keuzelijst voor de **periode** in de rechter bovenhoek van het dash board. 

Het plan Dashboard bevat de volgende informatie:

* **Plan beschrijving** geeft informatie weer over de kosten en de capaciteit die zijn gekoppeld aan het plan.
* Met het **plan gebruik** wordt het aantal trans acties en reken uren weer gegeven dat voor het plan in rekening is gebracht.
* **Webservices** geeft het aantal webservices weer dat dit abonnement gebruikt.
* **Met de bovenste webservice op aanroepen** worden de vier webservices weer gegeven die aanroepen voor het abonnement in rekening worden gebracht.
* De belangrijkste **webservices per reken uren** geeft de vier webservices weer die gebruikmaken van reken resources die worden gefactureerd op basis van het plan.

## <a name="manage-classic-web-services"></a>Klassieke webservices beheren
> [!NOTE]
> De procedures in deze sectie zijn relevant voor het beheer van klassieke webservices via de Azure Machine Learning Web Services-portal. Zie [een Azure machine learning Studio (klassieke) werk ruimte beheren](manage-workspace.md)voor meer informatie over het beheren van klassieke webservices via de machine learning Studio (klassiek) en de Azure Portal.
> 
> 

Uw klassieke webservices beheren:

1. Meld u aan bij de [Microsoft Azure machine learning Web Services](https://services.azureml.net/quickstart) -Portal met uw Microsoft Azure account: gebruik het account dat is gekoppeld aan het Azure-abonnement.
2. Klik in het menu op **klassieke webservices**.

Als u een klassieke webservice wilt beheren, klikt u op **klassieke** webservices. U kunt op de pagina klassiek Web Services het volgende doen:

* Klik op de webservice om de bijbehorende eind punten weer te geven.
* Een webservice verwijderen.

Wanneer u een klassieke webservice beheert, beheert u elk van de eind punten afzonderlijk. Wanneer u op een webservice klikt op de pagina webservices, wordt de lijst met eind punten die aan de service zijn gekoppeld, geopend. 

Op de pagina klassiek-webservice-eind punt kunt u eind punten toevoegen en verwijderen voor de service. Zie [eind punten maken](create-endpoint.md)voor meer informatie over het toevoegen van eind punten.

Klik op een van de eind punten om de pagina Web Service Quick Start te openen. Op de pagina Quick start ziet u twee menu opties waarmee u uw webservice kunt beheren:

* **Dash board** : Hiermee kunt u het gebruik van webservices weer geven.
* **Configureren** : Hiermee kunt u beschrijvende tekst toevoegen, fout logboek registratie in-en uitschakelen, de sleutel bijwerken voor het opslag account dat is gekoppeld aan de webservice en voorbeeld gegevens in-en uitschakelen.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Controleren hoe de webservice wordt gebruikt
Klik op het tabblad **dash board** .

Vanuit het dash board kunt u het totale gebruik van uw web-service gedurende een bepaalde periode bekijken. U kunt de periode selecteren die u wilt weer geven in de vervolg keuzelijst periode in de rechter bovenhoek van de gebruiks diagrammen. Het dash board bevat de volgende informatie:

* **Aanvragen in** de loop van de tijd geeft een stap grafiek weer van het aantal aanvragen gedurende de geselecteerde tijds periode. Zo kunt u nagaan of u problemen ondervindt met pieken in het gebruik.
* **Aanvraag-antwoord aanvragen** hier wordt het totale aantal Request-Response-aanroepen weer gegeven dat de service heeft ontvangen over de geselecteerde tijds periode en hoeveel er is mislukt.
* **Gemiddelde Request-Response reken tijd** toont een gemiddelde van de tijd die nodig is om de ontvangen aanvragen uit te voeren.
* Met **batch-aanvragen** wordt het totale aantal batch-aanvragen weer gegeven dat de service heeft ontvangen over de geselecteerde tijds periode en hoeveel er is mislukt.
* Bij **gemiddelde taak latentie** wordt een gemiddelde weer gegeven van de tijd die nodig is om de ontvangen aanvragen uit te voeren.
* **Fouten** geeft het totale aantal fouten weer dat is opgetreden bij aanroepen naar de webservice.
* Met **kosten voor services** worden de kosten weer gegeven voor het facturerings plan dat is gekoppeld aan de service.

### <a name="configuring-the-web-service"></a>De webservice configureren
Klik op de menu optie **configureren** .

U kunt de volgende eigenschappen bijwerken:

* Met **Beschrijving** kunt u een beschrijving invoeren voor de webservice. Beschrijving is een verplicht veld.
* Met **logboek registratie** kunt u fout logboek registratie op het eind punt in-of uitschakelen. Zie [logboek registratie inschakelen voor machine learning-webservices](web-services-logging.md)voor meer informatie over logboek registratie.
* Door **voorbeeld gegevens in te scha kelen** kunt u voorbeeld gegevens opgeven die u kunt gebruiken om de Request-Response-service te testen. Als u de webservice in Machine Learning Studio (klassiek) hebt gemaakt, worden de voorbeeld gegevens opgehaald uit de gegevens waarmee u uw model hebt getraind. Als u de service programmatisch hebt gemaakt, worden de gegevens opgehaald uit de voorbeeld gegevens die u hebt ingevoerd als onderdeel van het JSON-pakket.