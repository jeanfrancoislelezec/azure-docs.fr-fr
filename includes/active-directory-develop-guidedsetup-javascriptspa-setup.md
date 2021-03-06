---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 794fd51c38f66b24193c7da7a145d58f7a225b30
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
## <a name="setting-up-your-web-server-or-project"></a>Configuration du serveur web ou du projet

> Vous préférez télécharger le projet de cet exemple ? 
> - [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> or
> - [Télécharger les fichiers de projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) pour un serveur web local tel que Python
>
> Passez ensuite à l’étape [Configuration](#register-your-application) pour configurer l’exemple de code avant son exécution.

## <a name="prerequisites"></a>Prérequis

Pour exécuter cette installation guidée, vous avez besoin d’un serveur web local tel que [Python http-server](https://www.python.org/downloads/), [http-server](https://www.npmjs.com/package/http-server/) ou [.NET Core](https://www.microsoft.com/net/core), ou de l’intégration d’IIS Express à [Visual Studio 2017](https://www.visualstudio.com/downloads/). 

Les instructions de ce guide sont basées sur Python et Visual Studio 2017. Cependant, vous pouvez utiliser n’importe quel environnement de développement ou serveur web.

## <a name="create-your-project"></a>Créer votre projet 

> ### <a name="option-1-visual-studio"></a>Option 1 : Visual Studio 
> Si vous utilisez Visual Studio pour créer un projet, suivez les étapes ci-dessous pour créer une solution Visual Studio :
> 1.    Dans Visual Studio : `File` > `New` > `Project`
> 2.    Sous `Visual C#\Web`, sélectionnez `ASP.NET Web Application (.NET Framework)`
> 3.    Donnez un nom à votre application et cliquez sur *OK*.
> 4.    Sous `New ASP.NET Web Application`, sélectionnez `Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>Option 2 : Python / autres serveurs web
> Vérifiez que vous avez installé [Python](https://www.python.org/downloads/), puis suivez les étapes ci-dessous :
> - Créez un dossier pour héberger votre application.


## <a name="create-your-single-page-applications-ui"></a>Créer l’interface utilisateur de votre application à page unique (SPA)
1.  Créez un fichier *index.html* pour votre application SPA JavaScript. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, sélectionnez `Add` > `New Item` > `HTML page`, puis nommez-le index.html.
2.  Ajoutez le code suivant à votre page :
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
