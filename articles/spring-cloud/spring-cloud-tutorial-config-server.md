---
title: Uw exemplaar van een configuratieserver instellen in Azure Spring Cloud
description: Meer informatie over het instellen van een Spring Cloud-configuratieserver voor uw Azure Spring Cloud in de Azure-portal
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: 3033be3a793c318135f8150b86114b6fee55fac7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655195"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Een instantie van een Spring Cloud Config Server instellen voor uw service

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

In dit artikel leest u hoe u een instantie van een Spring Cloud Config Server kunt verbinden met uw Azure Spring Cloud-service.

Spring Cloud Config biedt ondersteuning aan server- en clientzijde voor een geëxternaliseerde configuratie in een gedistribueerd systeem. Met de instantie van Config Server heeft u een centrale plek om externe eigenschappen te beheren voor toepassingen in alle omgevingen. Raadpleeg [Spring Cloud Config Server-referentie](https://spring.io/projects/spring-cloud-config) voor meer informatie.

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 
* Een reeds ingerichte en actieve Azure Spring Cloud-service. Als u een Azure Spring Cloud-service wilt instellen en opstarten, bekijk dan [Quickstart: Een Java Spring-toepassing starten met behulp van de Azure CLI](spring-cloud-quickstart.md).

## <a name="restriction"></a>Beperking

Er gelden enkele beperkingen op het gebruik van Config Server met een Git-back-end. Sommige eigenschappen worden automatisch in de omgeving van uw toepassing om toegang te krijgen tot Config Server en Service Discovery. Als u deze eigenschappen ook vanuit uw Config Server-bestanden configureert, kunnen er conflicten en onverwacht gedrag optreden. De eigenschappen zijn: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> We raden u ten zeerste aan de bovenstaande eigenschappen _niet_ op te nemen in de toepassingsbestanden van uw Config Server.

## <a name="create-your-config-server-files"></a>Uw Config Server-bestanden maken

Azure Spring Cloud biedt ondersteuning voor Azure DevOps, GitHub, GitLab en Bitbucket om uw Config Server-bestanden op te slaan. Wanneer uw opslagplaats klaar is, maakt u de configuratiebestanden aan met de volgende instructies en slaat u ze daar op.

Daarnaast zijn sommige configureerbare eigenschappen alleen beschikbaar voor bepaalde typen. In de volgende subsecties vindt u de eigenschappen van elk type opslagplaats.

### <a name="public-repository"></a>Openbare opslagplaats

Wanneer u een openbare opslagplaats gebruikt, zijn uw configureerbare eigenschappen beperkter.

In de volgende tabel vindt u alle configureerbare eigenschappen die worden gebruikt om de openbare Git-opslagplaats in te stellen:

> [!NOTE]
> Het gebruik van een afbreekstreepje (-) als scheidingsteken tussen woorden is momenteel de enige ondersteunde naamconventie. U kunt bijvoorbeeld *standaard-label* gebruiken, maar niet *standaardLabel*.

| Eigenschap        | Vereist | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | De URI van de Git-opslagplaats die als back-end van de Config Server wordt gebruikt, begint met *http://* , *https://* , *git@* of *ssh://* . |
| `default-label` | Nee     | Het standaardlabel van de Git-opslagplaats moet de *vertakkingsnaam*, *tagnaam* of *commit-id* van de opslagplaat zijn. |
| `search-paths`  | Nee     | Een matrix van tekenreeksen die worden gebruikt om te zoeken in submappen van de Git-opslagplaats. |

------

### <a name="private-repository-with-ssh-authentication"></a>Persoonlijke opslagplaats met SSH-verificatie

In de volgende tabel vindt u alle configureerbare eigenschappen die worden gebruikt om de persoonlijke Git-opslagplaats in te stellen:

> [!NOTE]
> Het gebruik van een afbreekstreepje (-) als scheidingsteken tussen woorden is momenteel de enige ondersteunde naamconventie. U kunt bijvoorbeeld *standaard-label* gebruiken, maar niet *standaardLabel*.

| Eigenschap                   | Vereist | Functie                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Ja    | De URI van de Git-opslagplaats die als back-end van de Config Server wordt gebruikt, moet beginnen met *http://* , *https://* , *git@* of *ssh://* . |
| `default-label`            | Nee     | Het standaardlabel van de Git-opslagplaats moet de *vertakkingsnaam*, *tagnaam* of *commit-id* van de opslagplaat zijn. |
| `search-paths`             | Nee     | Een matrix van tekenreeksen gebruikt om te zoeken in submappen van de Git-opslagplaats. |
| `private-key`              | Nee     | De persoonlijke SSH-sleutel om de Git-opslagplaats te openen, _vereist_ wanneer de URI begint met *git@* of *ssh://* . |
| `host-key`                 | Nee     | De hostsleutel van de server voor de Git-opslagplaats mag het algoritmevoorvoegsel niet bevatten, zoals beschreven in `host-key-algorithm`. |
| `host-key-algorithm`       | Nee     | Het algoritme van de hostsleutel moet *ssh-dss-* zijn, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384* of *ecdsa-sha2-nistp521* zijn. Enkel *vereist* als `host-key` bestaat. |
| `strict-host-key-checking` | Nee     | Geeft aan of het opstarten van de instantie van de Config Server zal mislukken wanneer de persoonlijke `host-key` gebruikt wordt. Moet *true* (standaardwaarde) of *false* zijn. |

> [!NOTE]
> De configuratie server neemt `master` (om Git zelf) als standaard label als dat niet is opgegeven. Maar GitHub is de standaard vertakking gewijzigd van `master` in in `main` recent. Om te voor komen dat er een storing optreedt in de Cloud configuratie server van Azure, moet u letten op het standaard label bij het instellen van de configuratie server met GitHub, met name voor nieuwe gemaakte opslag plaatsen.

-----

### <a name="private-repository-with-basic-authentication"></a>Persoonlijke opslagplaats met basisverificatie

Hieronder vindt u alle configureerbare eigenschappen die worden gebruikt om de persoonlijke Git-opslagplaats met basisverificatie in te stellen.

> [!NOTE]
> Het gebruik van een afbreekstreepje (-) als scheidingsteken tussen woorden is momenteel de enige ondersteunde naamconventie. Gebruik bijvoorbeeld *standaard-label*, niet *standaardLabel*.

| Eigenschap        | Vereist | Functie                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | De URI van de Git-opslagplaats die als back-end van de Config Server wordt gebruikt, moet beginnen met *http://* , *https://* , *git@* of *ssh://* . |
| `default-label` | Nee     | Het standaardlabel van de Git-opslagplaats moet de *vertakkingsnaam*, *tagnaam* of *commit-id* van de opslagplaat zijn. |
| `search-paths`  | Nee     | Een matrix van tekenreeksen gebruikt om te zoeken in submappen van de Git-opslagplaats. |
| `username`      | Nee     | De gebruikersnaam die wordt gebruikt om toegang te krijgen tot de server van de Git-opslagplaats, _vereist_ wanneer de server van de Git-opslagplaats `Http Basic Authentication`ondersteunt. |
| `password`      | Nee     | Het wachtwoord dat wordt gebruikt om toegang te krijgen tot de server van de Git-opslagplaats, _vereist_ wanneer de server van de Git-opslagplaats `Http Basic Authentication`ondersteunt. |

> [!NOTE]
> Veel servers voor `Git`-opslagplaatsen bieden ondersteuning voor het gebruik van tokens in plaats van wachtwoorden voor HTTP-basisverificatie. Sommige opslagplaatsen, zoals GitHub staan toe om tokens voor onbepaalde tijd te gebruiken. Sommige servers voor Git-opslagplaatsen, zoals Azure DevOps, dwingen tokens om binnen enkele uren te vervallen. Opslagplaatsen die ervoor zorgen dat tokens verlopen, mogen geen verificatie op basis van tokens gebruiken met Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Git-opslagplaatsen met patroon

Hieronder vindt u alle configureerbare eigenschappen die worden gebruikt om Git-opslagplaatsen in te stellen met een patroon.

> [!NOTE]
> Het gebruik van een afbreekstreepje (-) als scheidingsteken tussen woorden is momenteel de enige ondersteunde naamconventie. Gebruik bijvoorbeeld *standaard-label*, niet *standaardLabel*.

| Eigenschap                           | Vereist         | Functie                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Nee             | Een toewijzing bestaande uit de instellingen voor een Git-opslagplaats met een bepaalde naam. |
| `repos."uri"`                      | Ja voor `repos` | De URI van de Git-opslagplaats die als back-end van de Config Server wordt gebruikt, moet beginnen met *http://* , *https://* , *git@* of *ssh://* . |
| `repos."name"`                     | Ja voor `repos` | Een naam om te identificeren in de Git-opslagplaats, alleen _vereist_ als `repos` bestaat. Bijvoorbeeld *team-A*, *team-B*. |
| `repos."pattern"`                  | Nee             | Een matrix van tekenreeksen gebruikt om een toepassingsnaam te vinden. Gebruik het `{application}/{profile}`-formaat met jokertekens voor elk patroon. |
| `repos."default-label"`            | Nee             | Het standaardlabel van de Git-opslagplaats moet de *vertakkingsnaam*, *tagnaam* of *commit-id* van de opslagplaat zijn. |
| `repos."search-paths`"             | Nee             | Een matrix van tekenreeksen gebruikt om te zoeken in submappen van de Git-opslagplaats. |
| `repos."username"`                 | Nee             | De gebruikersnaam die wordt gebruikt om toegang te krijgen tot de server van de Git-opslagplaats, _vereist_ wanneer de server van de Git-opslagplaats `Http Basic Authentication`ondersteunt. |
| `repos."password"`                 | Nee             | Het wachtwoord dat wordt gebruikt om toegang te krijgen tot de server van de Git-opslagplaats, _vereist_ wanneer de server van de Git-opslagplaats `Http Basic Authentication`ondersteunt. |
| `repos."private-key"`              | Nee             | De persoonlijke SSH-sleutel om de Git-opslagplaats te openen, _vereist_ wanneer de URI begint met *git@* of *ssh://* . |
| `repos."host-key"`                 | Nee             | De hostsleutel van de server voor de Git-opslagplaats mag het algoritmevoorvoegsel niet bevatten, zoals beschreven in `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | Nee             | Het algoritme van de hostsleutel moet *ssh-dss-* zijn, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384* of *ecdsa-sha2-nistp521* zijn. Enkel *vereist* als `host-key` bestaat. |
| `repos."strict-host-key-checking"` | Nee             | Geeft aan of het opstarten van de instantie van de Config Server zal mislukken wanneer de persoonlijke `host-key` gebruikt wordt. Moet *true* (standaardwaarde) of *false* zijn. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Uw Config Server-opslagplaats toevoegen aan Azure Spring Cloud

Nu dat uw configuratiebestanden zijn opgeslagen in een opslagplaats, moet u Azure Spring Cloud ermee verbinden.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Ga naar de **Overzichtspagina** van Azure Spring Cloud.

3. Selecteer **Configuratie server** in het navigatie deel venster aan de linkerkant.

4. Stel in de sectie **Standaardopslagplaats** **URI** in op https://github.com/Azure-Samples/piggymetrics-config.

5. Klik op **Valideren**.

    ![Navigeren naar configuratie server](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

6. Wanneer de validatie is voltooid, klikt u op **Toep assen** om uw wijzigingen op te slaan.

    ![De configuratie server wordt gevalideerd](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

7. Het bijwerken van de configuratie kan enkele minuten duren.
 
    ![De configuratie server wordt bijgewerkt](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

8. U ontvangt een melding wanneer de configuratie is voltooid.

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>De gegevens van de opslagplaats rechtstreeks invoeren bij de Azure-portal

#### <a name="default-repository"></a>Standaardopslagplaats

* **Openbare opslagplaats**: Plak de URI van de opslagplaats in het vak **URI** in de sectie **Standaardopslagplaats**.  Stel het **Label** in op **config**. Zorg ervoor dat de instelling **Verificatie** op **Openbaar** staat en selecteer vervolgen **Toepassen** om te voltooien. 

* **Persoonlijke opslagplaats**: Azure Spring Cloud biedt ondersteuning voor basisverificatie op basis van wachtwoorden/tokens en SSH.

    * **Basisverificatie**: Plak de URI van de opslagplaats in de sectie **Standaardopslagplaats** in het vak **URI** en selecteer vervolgens de knop **Verificatie** (pictogram van potlood). Selecteer **HTTP-basis** in het venster **Verificatie bewerken** in de vervolgkeuzelijst **Verificatietype** en voer vervolgens uw gebruikersnaam en wachtwoord/token in om toegang te verlenen tot Azure Spring Cloud. Selecteer **OK** en vervolgens **Toepassen** om de instelling van uw instantie van een Config Server te voltooien.

    ![Het deel venster verificatie bewerken basis verificatie](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Sommige servers voor Git-opslagplaatsen, zoals GitHub, gebruiken een *persoonlijk-token* of een *toegangs-token*, zoals een wachtwoord, voor **Basisverificatie**. U kunt dit soort token gebruiken als wachtwoord in Azure Spring Cloud aangezien het nooit verloopt. Maar voor andere servers voor Git-opslagplaatsen, zoals Bitbucket en Azure DevOps, verloopt het *toegangs-token* binnen één of twee uur. Dit betekent dat de optie niet werkbaar is wanneer u die servers voor opslagplaatsen gebruikt met Azure Spring Cloud.

    * **SSH**: Plak de URI van de opslagplaats in het vak **URI** in de sectie **Standaardopslagplaats** en selecteer vervolgens de knop **Verificatie** (pictogram van potlood). Selecteer **SSH** in de vervolgkeuzelijst **Verificatietype** in het deelvenster **Verificatie bewerken** en voer vervolgens uw **Persoonlijke sleutel** in. U kunt eventueel uw **Hostsleutel** en **Algoritme van de hostsleutel** opgeven. Zorg ervoor dat u uw openbare sleutel in uw opslagplaats voor de Config Server opneemt. Selecteer **OK** en vervolgens **Toepassen** om de instelling van uw instantie van een Config Server te voltooien.

    ![Het deel venster verificatie bewerken SSH auth](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Opslagplaats voor patronen

Als u een optionele **Opslagplaats voor patronen** wilt gebruiken om uw service te configureren, geef dan de **URI** en **Verificatie** op zoals voor de **Standaardopslagplaats**. Vergeet geen **Naam** op te geven voor uw patroon en selecteer vervolgens **Toepassen** om deze aan uw instantie toe te voegen. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Informatie over en opslagplaats invoeren in een YAML-bestand

Als u een YAML-bestand heeft geschreven met instellingen voor uw opslagplaats, dan kunt u het bestand rechtstreeks importeren vanaf uw lokale machine naar Azure Spring Cloud. Een eenvoudig YAML-bestand voor een persoonlijke opslagplaats met basisverificatie ziet er als volgt uit:

```yaml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Klik op de knop **Instellingen importeren** en selecteer vervolgens het YAML-bestand in uw projectmap. Selecteer **Importeren**, vervolgens verschijnt er een `async`-bewerking van uw **Meldingen**. Na 1-2 minuten zou de bewerking voltooid moeten zijn.

![Het deelvenster Meldingen van de Config Server](media/spring-cloud-tutorial-config-server/local-yml-success.png)


De informatie uit uw YAML-bestand zou moeten worden weergegeven in het Azure-portal. Selecteer **Toepassen** om te voltooien. 

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>Azure opslag plaatsen gebruiken voor Azure lente-Cloud configuratie

Azure lente Cloud kan toegang krijgen tot Git-opslag plaatsen die openbaar zijn, worden beveiligd door SSH of worden beveiligd met behulp van HTTP-basis verificatie. We zullen deze laatste optie gebruiken, omdat het eenvoudiger is om te maken en te beheren met Azure opslag plaatsen.

### <a name="get-repo-url-and-credentials"></a>Opslag plaats-URL en referenties ophalen
1. Klik in de Azure opslag plaatsen-portal voor uw project op de knop klonen:

    ![Knop klonen](media/spring-cloud-tutorial-config-server/clone-button.png)

1. Kopieer de kloon-URL uit het tekstvak. Deze URL heeft normaal gesp roken de volgende vorm:

    ```Text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Verwijder alles na `https://` en voor `dev.azure.com` , inclusief de `@` . De resulterende URL moet de volgende indeling hebben:

    ```Text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Sla deze URL op voor gebruik in de volgende sectie.

1. Klik op Git-Referenties genereren. Er wordt een gebruikers naam en wacht woord weer gegeven. Sla deze op voor gebruik in de volgende sectie.


### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>Azure Spring Cloud configureren voor toegang tot de Git-opslagplaats

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Ga naar de pagina **Overzicht** van Azure Spring Cloud.

1. Selecteer de service die u wilt configureren.

1. Selecteer in het linkerdeel venster van de service pagina onder **instellingen** het tabblad **Configuratie server** . Configureer de opslag plaats die we eerder hebben gemaakt:
   - Voeg de URL van de opslag plaats toe die u in de vorige sectie hebt opgeslagen
   - Klik op `Authentication` en selecteer `HTTP Basic`
   - De __gebruikers naam__ is de gebruikers naam die is opgeslagen in de vorige sectie
   - Het __wacht__ woord is het wacht woord dat u in de vorige sectie hebt opgeslagen
   - Klik op Toep assen en wacht totdat de bewerking is voltooid

   ![Spring Cloud-configuratieserver](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-app-configuration"></a>Uw app-configuratie verwijderen

Nadat u een configuratiebestand hebt opgeslagen verschijnt de knop **App-configuratie verwijderen** bij het tabblad **Configuratie**. Wanneer u deze knop selecteert wist u uw bestaande instellingen volledig. Gebruik deze om uw instantie van een Config Server te verbinden met een andere bron, bijvoorbeeld wanneer u overstapt van Github naar Azure DevOps.



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u uw exemplaar van een Spring Cloud-configuratieserver kunt inschakelen en configureren. Zie [Een toepassing schalen in Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md) voor meer informatie over het beheren van uw toepassing.
