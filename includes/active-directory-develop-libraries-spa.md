---
ms.openlocfilehash: a393cf978318c39081805cae7e38c3386ff156f5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010695"
---
| Taal/Framework | Project op<br/>GitHub                                                                                                    | Pakket                                                                      | Slaag<br/>helpen                             | Gebruikers aanmelden                                         | Toegang tot Web-Api's                                                 | Algemeen beschikbaar (GA) *of*<br/>Open bare preview<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL hoek v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Open bare preview                                               |
| Angular              | [MSAL hoek](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Zelfstudie](../articles/active-directory/develop/tutorial-v2-angular.md)| ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Open bare preview                                               |
| Javascript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Zelfstudie](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
|Javascript|[MSAL.js 1,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Zelfstudie](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| React                | [MSAL reageren](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Bibliotheek kan ID-tokens aanvragen voor gebruikers aanmelding.][y] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Open bare preview                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [aanvullende gebruiks voorwaarden voor Microsoft Azure previews][preview-tos] zijn van toepassing op bibliotheken in de *open bare preview*.

<sup>2</sup> - [verificatie code stroom][auth-code-flow] met alleen PCKE (aanbevolen). 

<sup>3</sup> [impliciete toekennings stroom][implicit-flow] .

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md