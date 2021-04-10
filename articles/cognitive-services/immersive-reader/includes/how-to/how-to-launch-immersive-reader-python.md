---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620075"
---
## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Een insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](../../how-to-create-immersive-reader.md) om deze in te stellen.  U hebt enkele waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* [Git](https://git-scm.com/).
* [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) en [pip](https://docs.python.org/3/installing/index.html). Vanaf Python 3.4 is pip standaard toegevoegd aan de binaire installatieprogramma's van Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/).
* [Jinja](http://jinja.pocoo.org/docs/2.10/).
* [virtualenv](https://virtualenv.pypa.io/en/latest/) en [virtualenvwrapper-win voor Windows](https://pypi.org/project/virtualenvwrapper-win/) of [virtualenvwrapper voor OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* De [request-module](https://pypi.org/project/requests/2.7.0/).
* Een code-editor, zoals [Visual Studio Code](https://code.visualstudio.com/).

## <a name="configure-authentication-credentials"></a>Verificatiereferenties configureren

Maak een nieuw bestand met de naam **.env** en plak de volgende namen en waarden in het bestand. Geef de waarden op die werden weergegeven bij het maken van de Insluitende lezer-resource.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Zorg ervoor dat u dit bestand niet doorvoert in broncodebeheer, omdat er geheimen in staan die niet openbaar mogen worden gemaakt.

Beveilig het **getimmersivereadertoken** API-eindpunt achter een vorm van verificatie, bijvoorbeeld [OAuth](https://oauth.net/2/). Verificatie voorkomt dat niet-geautoriseerde gebruikers tokens verkrijgen voor gebruik van uw Insluitende lezer-service en facturering. Dat werk valt buiten het bereik van deze zelfstudie.

## <a name="create-a-python-web-app-on-windows"></a>Een Python-web-app maken in Windows

Een Python-web-app maken door middel van `flask` in Windows.

Installeer [Git](https://git-scm.com/).

Nadat Git is geïnstalleerd, opent u een opdrachtprompt en kloont u de Git-opslagplaats van de SDK van de Insluitende lezer naar een map op uw computer.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installeer [Python](https://www.python.org/downloads/).

Schakel het selectievakje **Python toevoegen aan PATH** in.

![Dialoogvenster Python voor Windows installeren stap 1](../../media/pythoninstallone.jpg)

Voeg **Optionele functies** toe door selectievakjes in te schakelen en vervolgens **Volgende** te selecteren.

![Dialoogvenster Python voor Windows installeren stap 2](../../media/pythoninstalltwo.jpg)

Selecteer **Aangepaste installatie** en stel het installatiepad in als uw hoofdmap, bijvoorbeeld `C:\Python37-32\`. Selecteer vervolgens **Installeren**.

![Dialoogvenster Python voor Windows installeren stap 3](../../media/pythoninstallthree.jpg)

Nadat de Python-installatie is voltooid, opent u een opdrachtprompt en gebruikt u `cd` om naar de map Python Scripts te gaan.

```cmd
cd C:\Python37-32\Scripts
```

Installeer Flask.

```cmd
pip install flask
```

Installeer Jinja2. Het is een sjabloonengine met volledige functionaliteit voor Python.

```cmd
pip install jinja2
```

Installeer virtualenv. Met dit hulpprogramma worden geïsoleerde Python-omgevingen gemaakt.

```cmd
pip install virtualenv
```

Installeer virtualenvwrapper-win. Het idee achter virtualenvwrapper is om het gebruik van virtualenv te vereenvoudigen.

```cmd
pip install virtualenvwrapper-win
```

Installeer de requests-module. Requests is een voor Apache2 gelicentieerde HTTP-bibliotheek, geschreven in Python.

```cmd
pip install requests
```

Installeer de python-dotenv-module. Deze module leest het sleutel-waardepaar van het .env-bestand en voegt dit toe aan de omgevingsvariabele.

```cmd
pip install python-dotenv
```

Een virtuele omgeving maken.

```cmd
mkvirtualenv advanced-python
```

Gebruik `cd` om naar de hoofdmap van het voorbeeldproject te gaan.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Verbind het voorbeeldproject met de omgeving. Met deze actie wordt de zojuist gemaakte virtuele omgeving toegewezen aan de basismap van het voorbeeldproject.

```cmd
setprojectdir .
```

Activeer de virtuele omgeving.

```cmd
activate
```

Het project moet nu actief zijn en u ziet iets als `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` in de opdrachtprompt.

Deactiveer de omgeving.

```cmd
deactivate
```

Het voorvoegsel `(advanced-python)` moet worden verwijderd omdat de omgeving is gedeactiveerd.

Voer `workon advanced-python` uit vanuit de hoofdmap van het voorbeeldproject om de omgeving opnieuw te activeren.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Insluitende lezer starten met voorbeeldinhoud

Wanneer de omgeving actief is, voert u het voorbeeldproject uit door `flask run` vanuit de hoofdmap van het voorbeeldproject in te voeren.

```cmd
flask run
```

Open uw browser en ga naar http://localhost:5000.

## <a name="create-a-python-web-app-on-osx"></a>Een Python-web-app maken in OSX

Maak een Python-web-app door middel van `flask` in OSX.

Installeer [Git](https://git-scm.com/).

Nadat Git is geïnstalleerd, opent u Terminal en kloont u de Git-opslagplaats van de SDK van de Insluitende lezer naar een map op uw computer.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installeer [Python](https://www.python.org/downloads/).

De Python-hoofdmap, bijvoorbeeld `Python37-32`, moet zich nu in de map Toepassingen bevinden.

Nadat de Python-installatie is voltooid, opent u Terminal en gebruikt u `cd` om naar de map Python Scripts te gaan.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Pip installeren.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Voer de volgende code uit om pip te installeren voor de gebruiker die momenteel is aangemeld om machtigingsproblemen te voorkomen.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Als dit wordt gevraagd, voert u uw wachtwoord in.
- Voeg het pad van de pip-installatie toe aan de padvariabele.
- Ga naar de onderkant van het bestand en geef het pad dat u wilt toevoegen als laatste item in de lijst op, bijvoorbeeld `PATH=$PATH:/usr/local/bin`.
- Selecteer **CTRL+X** om af te sluiten.
- Voer **Y**  in om de gewijzigde buffer op te slaan.
- Dat is alles. U kunt dit testen door `echo $PATH` in te voeren in een nieuw Terminal-venster.

Installeer Flask.

```bash
pip install flask --user
```

Installeer Jinja2. Het is een sjabloonengine met volledige functionaliteit voor Python.

```bash
pip install Jinja2 --user
```

Installeer virtualenv. Met dit hulpprogramma worden geïsoleerde Python-omgevingen gemaakt.

```bash
pip install virtualenv --user
```

Installeer virtualenvwrapper. Het idee achter virtualenvwrapper is om het gebruik van virtualenv te vereenvoudigen.

```bash
pip install virtualenvwrapper --user
```

Installeer de requests-module. Requests is een voor Apache2 gelicentieerde HTTP-bibliotheek, geschreven in Python.

```bash
pip install requests --user
```

Installeer de python-dotenv-module. Deze module leest het sleutel-waardepaar van het .env-bestand en voegt dit toe aan de omgevingsvariabele.

```bash
pip install python-dotenv --user
```

Kies een map waarin u uw virtuele omgevingen wilt opslaan en voer deze opdracht uit:

```bash
mkdir ~/.virtualenvs
```

Gebruik `cd` om naar de Python-map met de voorbeeldtoepassing van de Insluitende lezer-SDK te gaan.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Een virtuele omgeving maken.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Verbind het voorbeeldproject met de omgeving. Met deze actie wordt de zojuist gemaakte virtuele omgeving toegewezen aan de basismap van het voorbeeldproject.

```bash
setprojectdir .
```

Activeer de virtuele omgeving.

```bash
activate
```

Het project moet nu actief zijn en u ziet iets als `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` in de opdrachtprompt.

Deactiveer de omgeving.

```bash
deactivate
```

Het voorvoegsel `(advanced-python)` moet worden verwijderd omdat de omgeving is gedeactiveerd.

Voer `workon advanced-python` uit vanuit de hoofdmap van het voorbeeldproject om de omgeving opnieuw te activeren.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Insluitende lezer starten met voorbeeldinhoud

Wanneer de omgeving actief is, voert u het voorbeeldproject uit door `flask run` vanuit de hoofdmap van het voorbeeldproject in te voeren.

```bash
flask run
```

Open uw browser en ga naar http://localhost:5000.

## <a name="next-steps"></a>Volgende stappen

* Verken de [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](../../reference.md).
* Bekijk codevoorbeelden op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
