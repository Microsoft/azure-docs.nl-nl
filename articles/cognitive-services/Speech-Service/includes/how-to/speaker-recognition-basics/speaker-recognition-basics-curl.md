---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3a32946e10e8a81394b54fc44e4c92e8625e7ad6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015479"
---
In deze quickstart leert u basispatronen voor het ontwerp van Speaker Recognition met behulp van de Speech SDK, met inbegrip van:

* Tekstafhankelijke en tekstonafhankelijke verificatie
* Sprekeridentificatie voor het identificeren van een spraaksample in een groep stemmen
* Spraakprofielen verwijderen

Zie het artikel [Overzicht](../../../speaker-recognition-overview.md) voor een diepgaande bespreking van de concepten van spraakherkenning.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Speaker Recognition wordt momenteel *alleen* ondersteund in Azure Speech-resources die in de regio `westus` zijn gemaakt.

## <a name="text-dependent-verification"></a>Tekstafhankelijke verificatie

Met Speaker Verification wordt bevestigd dat een spreker overeenkomt met een bekende of **geregistreerde** stem. De eerste stap bestaat uit het **registreren** van een spraakprofiel, zodat dit met latere spraaksamples kan worden vergeleken. In dit voorbeeld moet u het profiel registreren door middel van een **tekstafhankelijke** strategie, waarvoor een specifieke wachtwoordzin is vereist voor zowel de registratie als de verificatie. Zie het [referentiemateriaal ](/rest/api/speakerrecognition/) voor een lijst met ondersteunde wachtwoordzinnen.

Eerst moet u [een spraakprofiel maken](/rest/api/speakerrecognition/verification/textdependent/createprofile). U moet de abonnementssleutel en regio voor uw spraakservice invoegen in elke cURL-opdracht in dit artikel.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Er zijn drie typen spraakprofielen:

- Tekstafhankelijke verificatie
- Tekstonafhankelijke verificatie
- Tekstonafhankelijke identificatie

In dit geval maakt u een spraakprofiel van het type tekstafhankelijke verificatie. U ontvangt het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Vervolgens [registreert u het spraakprofiel](/rest/api/speakerrecognition/verification/textdependent/createenrollment). Geef voor de parameterwaarde `--data-binary` een audiobestand op uw computer op dat een van de ondersteunde wachtwoordzinnen bevat, zoals 'mijn stem is mijn paspoort, verifieer mij'. U kunt een dergelijk audiobestand opnemen met een app, zoals [Windows Voicerecorder](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab), of u kunt deze genereren met behulp van [tekst-naar-spraak](../../../index-text-to-speech.yml).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

U ontvangt het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Dit antwoord vertelt u dat u nog twee audiosamples moet registreren.

Als u in totaal drie audiosamples hebt geregistreerd, ontvangt u het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Nu bent u klaar om [een audiosample te verifiëren met het spraakprofiel](/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Dit audiosample moet dezelfde wachtwoordzin bevatten als de samples die u hebt gebruikt om het spraakprofiel te registreren.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

U ontvangt het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept` betekent dat de wachtwoordzin overeenkomt en de verificatie is geslaagd. Het antwoord bevat ook een vergelijkingsscore, die kan variëren van 0,0 tot 1,0.

Tot slot [verwijdert u het spraakprofiel](/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

U ontvangt geen antwoord.

## <a name="text-independent-verification"></a>Tekstonafhankelijke verificatie

In tegenstelling tot **tekstafhankelijke** verificatie, geldt voor **tekstonafhankelijke** verificatie:

* Vereist niet dat een bepaalde wachtwoordzin wordt uitgesproken; alles mag worden uitgesproken
* Vereist geen drie audiosamples, maar vereist in totaal *wel* twintig seconden aan audio

Eerst [maakt u een profiel voor tekstonafhankelijke verificatie](/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

U ontvangt het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

Vervolgens [registreert u het spraakprofiel](/rest/api/speakerrecognition/verification/textindependent/createenrollment). In plaats van drie audiosamples in te dienen, moet u audiosamples met in totaal 20 seconden audio indienen.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Als u genoeg audiosamples hebt geregistreerd, ontvangt u het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Nu bent u klaar om [een audiosample te verifiëren met het spraakprofiel](/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Nogmaals, dit audiosample hoeft geen wachtwoordzin te bevatten. Het kan willekeurige spraak bevatten, zolang deze in een totaal ten minste vier seconden aan audio bevat.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

U ontvangt het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept` betekent dat de verificatie is geslaagd. Het antwoord bevat ook een vergelijkingsscore, die kan variëren van 0,0 tot 1,0.

Tot slot [verwijdert u het spraakprofiel](/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

U ontvangt geen antwoord.

## <a name="speaker-identification"></a>Sprekeridentificatie

Speaker Identification wordt gebruikt om vast te stellen **wie** er spreekt uit een bepaalde groep geregistreerde stemmen. Het proces is vergelijkbaar met **tekstonafhankelijke verificatie**, waarbij het belangrijkste verschil is dat tegelijkertijd verificatie tegen meerdere spraakprofielen kan worden uitgevoerd in plaats van te verifiëren tegen één profiel.

Eerst [maakt u een profiel voor tekstonafhankelijke identificatie](/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

U ontvangt het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Vervolgens [registreert u het spraakprofiel](/rest/api/speakerrecognition/identification/textindependent/createenrollment). Nogmaals, u moet audiosamples met in totaal twintig seconden audio indienen. Deze samples hoeven geen wachtwoordzin te bevatten.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Als u genoeg audiosamples hebt geregistreerd, ontvangt u het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Nu bent u klaar om [een audiosample te identificeren met het spraakprofiel](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). De identificatieopdracht accepteert een door komma's gescheiden lijst met mogelijke spraakprofiel-id's. In dit geval geeft u alleen de id op van het spraakprofiel dat u eerder hebt gemaakt. Als u wilt, kunt u echter meerdere spraakprofiel-id's doorgeven, waarbij elk spraakprofiel is ingeschreven met audiosamples van een andere stem.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

U ontvangt het volgende antwoord.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

Het antwoord bevat de id van het spraakprofiel dat het meest overeenkomt met het audiosample dat u hebt ingediend. Het bevat ook een lijst met kandidaat-spraakprofielen, gerangschikt op volgorde van overeenkomst.

Tot slot [verwijdert u het spraakprofiel](/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

U ontvangt geen antwoord.