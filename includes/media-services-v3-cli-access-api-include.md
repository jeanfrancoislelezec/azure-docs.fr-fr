---
title: Fichier Include
description: Fichier Include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: acb9bdf294dd66005df203f957c155540b658698
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33830100"
---
## <a name="access-the-media-services-api"></a>Accéder à l’API Media Services

Vous utilisez l’authentification de principal de service Azure AD pour vous connecter aux API Azure Media Services. La commande suivante crée une application Azure AD et attache un principal de service pour le compte. Vous vous apprêtez à utiliser les valeurs renvoyées pour configurer votre application .NET, comme indiqué dans l’étape suivante.

Avant d’exécuter le script, vous pouvez remplacer le `amsaccount` et `amsResourceGroup` par les noms que vous avez choisis lors de la création de ces ressources. `amsaccount` est le nom du compte Azure Media Services auquel attacher le principal de service. <br/>La commande suivante utilise l’option `xml` qui retourne un document xml que vous pouvez coller dans votre fichier app.config. Si vous omettez l’option `xml`, la réponse sera dans `json`.

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

Cette commande génère une réponse semblable à celle-ci :

```xml
<add key="Region" value="West US 2" />
<add key="ResourceGroup" value="amsResourceGroup" />
<add key="AadEndpoint" value="https://login.microsoftonline.com" />
<add key="AccountName" value="amsaccount" />
<add key="SubscriptionId" value="111111111-0000-2222-3333-55555555555" />
<add key="ArmAadAudience" value="https://management.core.windows.net/" />
<add key="AadTenantId" value="2222222222-0000-2222-3333-6666666666666" />
<add key="AadSecret" value="33333333-0000-2222-3333-55555555555" />
<add key="AadClientId" value="44444444-0000-2222-3333-55555555555" />
<add key="ArmEndpoint" value="https://management.azure.com/" />
```

### <a name="configure-the-sample-app"></a>Configurer l’exemple d’application

Pour exécuter l’application et accéder aux API de Media Services, vous devez spécifier les valeurs d’accès correct dans le fichier App.config. 

1. Ouvrez Visual Studio.
2. Accédez à la solution que vous avez clonée.
3. Dans l’Explorateur de solutions, déroulez le projet *EncodeAndStreamFiles*.
4. Définissez ce projet comme projet de démarrage.
5. Ouvrez le fichier App.config.
6. Remplacez les valeurs appSettings par les valeurs obtenues à l’étape précédente.

 ```xml
 <add key="Region" value="value" />
 <add key="ResourceGroup" value="value" />
 <add key="AadEndpoint" value="value" />
 <add key="AccountName" value="value" />
 <add key="SubscriptionId" value="value" />
 <add key="ArmAadAudience" value="value" />
 <add key="AadTenantId" value="value" />
 <add key="AadSecret" value="value" />
 <add key="AadClientId" value="value" />
 <add key="ArmEndpoint" value="value" />
 ```    
 
7. Appuyez sur Ctrl+Maj+B pour générer la solution.
