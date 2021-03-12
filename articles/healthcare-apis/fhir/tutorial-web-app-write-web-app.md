---
title: Zelfstudie voor web-apps - Webtoepassing schrijven
description: Deze zelfstudie laat zien hoe u een eenvoudige webtoepassing implementeert. In deze sectie van de zelfstudie wordt uitgelegd hoe u de web-app schrijft.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018470"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>Een Azure-webtoepassing schrijven om FHIR-gegevens te lezen
Nu u verbinding kunt maken met uw FHIR-server en gegevens kunt plaatsen, bent u klaar om een webtoepassing te schrijven waarmee FHIR-gegevens worden gelezen. In deze laatste stap van de zelfstudie wordt uitgelegd hoe u de webtoepassing gaat schrijven en gebruiken.

## <a name="create-web-application"></a>Webtoepassing maken
Selecteer in Azure **Een resource maken** en selecteer **web-app**. Zorg ervoor dat u uw webtoepassing een naam geeft, ongeacht wat u hebt opgegeven in de omleidings-URI voor uw clienttoepassing of ga terug en werk de omleidings-URI bij met de nieuwe naam. 

![Webtoepassing maken](media/tutorial-web-app/create-web-app.png)

Zodra de webtoepassing beschikbaar is, **gaat u naar de resource**. Selecteer **App Service Editor (preview)** onder ontwikkelingsprogramma's aan de rechterkant en selecteer vervolgens **Go**. Als u Go selecteert, wordt de App Service Editor geopend. Klik met de rechtermuisknop op de grijze ruimte onder *Verkennen* en maak een nieuw bestand met de naam **index. html-** .

Hieronder ziet u de code die u kunt invoeren in **index. html**. U moet de volgende items bijwerken:
* **clientId-** : werk bij met uw clienttoepassings-id. Deze id is dezelfde id die u hebt opgehaald bij het ophalen van uw token
* **instantie**: werk bij met uw Azure AD tenant-id
* **FHIRendpoint**: werk de FHIRendpoint bij om uw FHIR-servicenaam te verkrijgen
* **scopes** :werk bij om de volledige URL voor uw doelgroep weer te geven

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

Hier kunt u teruggaan naar de resource van uw webtoepassing en de URL openen die op de pagina Overzicht wordt weergegeven. Meld u aan om de patiënt James Tiberious Kirk te zien die u eerder hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen
U hebt de Azure API for FHIR geïmplementeerd, een openbare clienttoepassing, een geteste toegang en een kleine webtoepassing gemaakt. Bekijk de Azure API for FHIR-ondersteunde functies als volgende stap.

>[!div class="nextstepaction"]
>[Ondersteunde functies](fhir-features-supported.md)





