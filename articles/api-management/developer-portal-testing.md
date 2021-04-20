---
title: De zelf-hostende ontwikkelaarsportal testen
titleSuffix: Azure API Management
description: Meer informatie over het instellen van eenheidstests en end-to-end-tests voor uw zelf-hostende API Management portal.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ab6a7aa8fc4f11c34126415379294ef1e48fa286
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741712"
---
# <a name="test-the-self-hosted-developer-portal"></a>De zelf-hostende ontwikkelaarsportal testen

In dit artikel wordt uitgelegd hoe u eenheidstests en end-to-end-tests in kunt stellen voor uw [zelf-hostende portal.](developer-portal-self-host.md)

## <a name="unit-tests"></a>Eenheidstests

Een eenheidstest is een benadering om kleine onderdelen van de functionaliteit te valideren. Dit gebeurt geïsoleerd van andere onderdelen van de toepassing.

### <a name="example-scenario"></a>Voorbeeldscenario

In dit scenario test u een besturingselement voor wachtwoordinvoer. Er worden alleen wachtwoorden geaccepteerd die ten minste het volgende bevatten:

- Eén letter

- Eén getal

- Eén speciaal teken
 
De test voor het valideren van deze vereisten ziet er als volgende uit:

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Projectstructuur

Het is gebruikelijk om een eenheidstest naast het onderdeel te houden dat moet worden gevalideerd.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>HTTP-aanvragen na te maken

Er zijn gevallen waarin u verwacht dat een onderdeel HTTP-aanvragen zal doen. Het onderdeel moet op de juiste manier reageren op verschillende soorten reacties. Gebruik om specifieke HTTP-antwoorden te `MockHttpClient` simuleren. Het implementeert de `HttpClient` interface die wordt gebruikt door veel andere onderdelen van het project.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>End-to-end-tests

Een end-to-end-test voert een bepaald gebruikersscenario uit door exacte stappen uit te voeren die de gebruiker verwacht uit te voeren. In een webtoepassing zoals te Azure API Management-ontwikkelaarsportal bladert de gebruiker door de inhoud en selecteert hij opties om bepaalde resultaten te behalen. 

Als u gebruikersnavigatie wilt repliceren, kunt u helperbibliotheken voor browserbewerkingen gebruiken, [zoals Puppeteer](https://github.com/puppeteer/puppeteer). Hiermee kunt u gebruikersacties simuleren en veronderstelde scenario's automatiseren. Puppeteer maakt ook automatisch schermopnamen van pagina's of onderdelen in elke fase van de test. Vergelijk deze later met eerdere resultaten om afwijkingen en mogelijke regressies te ondervangen.

### <a name="example-scenario"></a>Voorbeeldscenario

In dit scenario moet u een gebruikers aanmeldingsstroom valideren. Voor dit scenario zijn de volgende stappen vereist:

1. Open de browser en navigeer naar de aanmeldingspagina.

1. Voer het e-mailadres in.

1. Voer het wachtwoord in.

1. Selecteer **Aanmelden.**

1. Controleer of de gebruiker is omgeleid naar de startpagina.

1. Controleer of de pagina het menu-item **Profiel** bevat. Het is een van de mogelijke indicatoren dat u zich hebt aangemeld.

Als u de test automatisch wilt uitvoeren, maakt u een script met exact dezelfde stappen:

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> Tekenreeksen zoals '#email', '#password' en '#signin' zijn CSS-achtige selectors die HTML-elementen op de pagina identificeren. Zie de [W3C-specificatie Selectors Level 3](https://www.w3.org/TR/selectors-3/) voor meer informatie.

### <a name="ui-component-maps"></a>Ui-onderdeelkaarten

Gebruikersstromen gaan vaak via dezelfde pagina's of onderdelen. Een goed voorbeeld is het hoofdmenu van de website dat op elke pagina aanwezig is. 

Maak een ui-onderdeelkaart om te voorkomen dat dezelfde selectors voor elke test worden geconfigureerd en bijgewerkt. U kunt bijvoorbeeld stap 2 tot en met 6 in het vorige voorbeeld vervangen door slechts twee regels:

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Configuratie testen

Voor bepaalde scenario's zijn mogelijk vooraf gemaakte gegevens of configuratie vereist. U moet bijvoorbeeld het aanmelden van gebruikers met sociale media-accounts automatiseren. Het is moeilijk om die gegevens snel of eenvoudig te maken.

Hiervoor kunt u een speciaal configuratiebestand toevoegen aan uw testscenario. De testscripts kunnen de vereiste gegevens uit het bestand ophalen. Afhankelijk van de build- en testpijplijn kunnen de tests de geheimen uit een benoemd beveiligd opslagopslag halen.

Hier is een voorbeeld van een `validate.config.json` die wordt opgeslagen in de `src` map van uw project.

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Headless versus normale tests

Met moderne browsers, zoals Chrome of Microsoft Edge kunt u automatisering uitvoeren in zowel de headless modus als de normale modus. De browser werkt zonder een grafische gebruikersinterface in de headless modus. Er worden nog steeds dezelfde pagina- en dombewerkingen Document Object Model uitgevoerd. De gebruikersinterface van de browser is doorgaans niet nodig in leveringspijplijnen. In dat geval is het uitvoeren van tests in de headless modus een uitstekende optie.

Wanneer u een testscript ontwikkelt, is het handig om te zien wat er precies gebeurt in de browser. Dit is een goed moment om de normale modus te gebruiken.

Als u wilt schakelen tussen de modi, wijzigt u de `headless` optie in het `constants.ts` bestand. Deze staat in de `tests` map in uw project:

```typescript
export const LaunchOptions = {
    headless: false
};
```

Een andere handige optie is `slowMo` . De uitvoering van de test tussen elke actie wordt onderbroken:

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Tests uitvoeren

Er zijn twee ingebouwde manieren om tests uit te voeren in dit project:

**npm-opdracht**

```console
npm run test
```

**Test Explorer**

De Test Explorer-extensie voor VS Code (bijvoorbeeld [Mocha Test Explorer](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)) heeft een handige gebruikersinterface en een optie om automatisch tests uit te voeren bij elke wijziging van de broncode:

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Schermopname van Visual Studio Code Test Explorer":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ontwikkelaarsportal:

- [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)

- [De ontwikkelaarsportal zelf hosten](developer-portal-self-host.md)
