---
title: 'Een tenantmodel maken (preview): spraakservice'
titleSuffix: Azure Cognitive Services
description: Genereer automatisch een beveiligd, compatibel tenantmodel (Custom Speech met Microsoft 365-gegevens) dat uw Microsoft 365-gegevens gebruikt voor het leveren van optimale spraakherkenning voor specifieke termen van de organisatie.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48cde51ee9941f705aa848d121c419a8f0c9ad1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013719"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Zelfstudie: Een tenantmodel maken (preview)

Tenant Model (Custom Speech met Microsoft 365-gegevens) is een opt-in-service voor Microsoft 365 Enterprise-klanten waarmee automatisch een aangepast spraakherkenningsmodel wordt gegenereerd op basis van de Microsoft 365-gegevens van uw organisatie. Het model is geoptimaliseerd voor technische termen, jargon en namen van personen, allemaal op een veilige en compatibele manier.

> [!IMPORTANT]
> Als uw organisatie zich registreert met behulp van de Tenant Model-service, kan de spraakservice toegang krijgen tot het taalmodel van uw organisatie. Het model wordt gegenereerd op basis van openbare e-mailberichten en documenten van Microsoft 365-groepen, die zichtbaar zijn voor iedereen in uw organisatie. De Microsoft 365-beheerder van uw organisatie kan het gebruik van het organisatiebrede taalmodel in- of uitschakelen vanuit de Microsoft 365-beheerportal.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Registreren in Tenant Model met behulp van het Microsoft 365-beheercentrum
> * Een Spraak-abonnementssleutel ophalen
> * Een tenantmodel maken
> * Een tenantmodel implementeren
> * Uw tenantmodel gebruiken met de Spraak-SDK

## <a name="enroll-in-the-tenant-model-service"></a>Registreren in de Tenant Model-service

Voordat u uw tenantmodel kunt implementeren, moet u zich registreren in de Tenant Model-service. De inschrijving moet worden gedaan in het Microsoft 365-beheercentrum en kan alleen worden uitgevoerd door uw beheerder.

1. Meld u aan bij het [Microsoft 365-beheercentrum](https://admin.microsoft.com).

1. Selecteer **Instellingen** in het linkerdeelvenster, selecteer **Instellingen** in het geneste menu, en selecteer vervolgens **Azure Speech Services** in het hoofdvenster.

   ![Het deelvenster Services en invoegtoepassingen](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Schakel het selectievakje **Organisatiebreed taalmodel toestaan** in en selecteer **Wijzigingen opslaan**.

   ![Het deelvenster Azure Speech Services](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Het tenantmodelexemplaar uitschakelen:
1. Herhaal de voorgaande stappen 1 en 2.
1. Schakel het selectievakje **Organisatiebreed taalmodel toestaan** uit en selecteer **Wijzigingen opslaan**.

## <a name="get-a-speech-subscription-key"></a>Een Spraak-abonnementssleutel ophalen

Als u uw tenantmodel wilt gebruiken met de Spraak-SDK, hebt u een Spraak-resource en de bijbehorende abonnementssleutel nodig.

1. Meld u aan bij de [Azure-portal](https://aka.ms/azureportal).
1. Selecteer **Een resource maken**.
1. In het vak **Zoeken** typt u **Spraak**.
1. Selecteer **Spraak** in de lijst met resultaten en selecteer vervolgens **Maken**.
1. Volg de aanwijzingen op het scherm om uw resource te maken. Zorg voor het volgende:
   * **Locatie** is ingesteld op **eastus** (US - oost) of **westus** (US - west).
   * **Prijscategorie** is ingesteld op **S0**.
1. Selecteer **Maken**.

   Na een paar minuten is uw resource gemaakt. De abonnementssleutel is beschikbaar in de sectie **Overzicht** voor uw resource.

## <a name="create-a-language-model"></a>Een taalmodel maken

Nadat de beheerder Tenant Model heeft ingeschakeld voor uw organisatie, kunt u een taalmodel maken op basis van uw Microsoft 365-gegevens.

1. Meld u aan bij [Speech Studio](https://speech.microsoft.com/).
1. Selecteer rechtsboven **Instellingen** (tandwielpictogram) en selecteer vervolgens **Instellingen voor tenantmodel**.

   ![De koppeling Instellingen voor tenantmodel](media/tenant-language-model/tenant-language-settings.png)

   In Speech Studio wordt een bericht weergegeven waarin u kunt zien of u een tenantmodel mag maken.

   > [!NOTE]
   > Microsoft 365 Enterprise-klanten in Noord-Amerika komen in aanmerking voor het maken van een tenantmodel (Engels). Als u een Klanten-lockbox-, Klantsleutel- of Office 365 Government-klant bent, is deze functie niet beschikbaar. Als u wilt bepalen of u een Klanten-lockbox- of Klantsleutel-klant bent, raadpleegt u:
   > * [Klanten-lockbox](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Klantsleutel](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 Government](https://www.microsoft.com/microsoft-365/government)

1. Selecteer **Aanmelden**.

   Wanneer uw tenantmodel gereed is, ontvangt u per e-mail een bevestigingsbericht met verdere instructies.

## <a name="deploy-your-tenant-model"></a>Uw tenantmodel implementeren

Wanneer uw tenantmodelexemplaar gereed is, implementeert u deze als volgt:

1. Selecteer in het bevestigingsbericht de knop **Model weergeven**. Of meld u aan bij [Speech Studio](https://speech.microsoft.com/).
1. Selecteer rechtsboven **Instellingen** (tandwielpictogram) en selecteer vervolgens **Instellingen voor tenantmodel**.

   ![De koppeling Instellingen voor tenantmodel](media/tenant-language-model/tenant-language-settings.png)

1. Selecteer **Implementeren**.

   Wanneer uw model is geïmplementeerd, wordt de status gewijzigd in *Geïmplementeerd*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Uw tenantmodel gebruiken met de Spraak-SDK

Nu u het model hebt geïmplementeerd, kunt u het gebruiken met de Spraak-SDK. In deze sectie gebruikt u voorbeeldcode om de spraakservice aan te roepen met behulp van Azure Active Directory-verificatie (Azure AD).

Laten we eens kijken naar de code die u gaat gebruiken om de Spraak-SDK aan te roepen in C#. In dit voorbeeld voert u spraakherkenning uit met behulp van uw tenantmodel. In deze gids wordt ervan uitgegaan dat uw platform al is ingesteld. Als u hulp nodig hebt bij het instellen, raadpleegt u [Quickstart: Spraakherkenning, C# (.NET Core)](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).

Kopieer deze code naar uw project:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Vervolgens moet u het project opnieuw opbouwen en uitvoeren vanaf de opdrachtregel. Voordat u de opdracht uitvoert, moet u een aantal parameters als volgt bijwerken:

1. Vervang `<Username>` en `<Password>` door de waarden voor een geldige tenantgebruiker.
1. Vervang `<Subscription-Key>` door de abonnementssleutel voor uw Spraak-resource. Deze waarde is beschikbaar in de sectie **Overzicht** voor uw Spraak-resource in [Azure Portal](https://aka.ms/azureportal).
1. Vervang `<Endpoint-Uri>` door het volgende eindpunt. Zorg ervoor dat u `{your region}` vervangt door de regio waarin uw Spraak-resource is gemaakt. De volgende regio's worden ondersteund: `westus`, `westus2` en `eastus`. Informatie over uw regio is beschikbaar in de sectie **Overzicht** van uw Spraak-resource in [Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Voer de volgende opdracht uit:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

In deze zelfstudie hebt u geleerd hoe u Microsoft 365-gegevens kunt gebruiken om een aangepast spraakherkenningsmodel te maken, te implementeren en te gebruiken met de Spraak-SDK.

## <a name="next-steps"></a>Volgende stappen

* [Speech Studio](https://speech.microsoft.com/)
* [Speech-SDK](speech-sdk.md)