---
title: Configurer le flux des informations d’identification par mot de passe du propriétaire de ressource dans Azure AD B2C | Microsoft Docs
description: Découvrez comment configurer le flux des informations d’identification par mot de passe du propriétaire de ressource dans Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.openlocfilehash: c1b4d641f6830751e2cb9e66d5052eb20a48d371
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158252"
---
# <a name="configure-the-resource-owner-password-credentials-flow-ropc-in-azure-ad-b2c"></a>Configurer le flux des informations d’identification par mot de passe du propriétaire de ressource dans Azure AD B2C

Le flux des informations d’identification par mot de passe du propriétaire de ressource est un flux d’authentification standard OAUTH où l’application, également appelée la partie de confiance, échange des informations d’identification valides comme un identifiant utilisateur et un mot de passe avec un jeton d’ID, un jeton d’accès et un jeton d’actualisation. 

> [!NOTE]
> Cette fonctionnalité est en préversion.

Dans Azure AD B2C, ces options sont prises en charge :

- **Client natif** : L’intervention de l’utilisateur pendant l’authentification se produit à l’aide du code qui s’exécute sur un appareil côté utilisateur, notamment une application mobile qui s’exécute dans le système d’exploitation natif comme Android ou dans le navigateur comme Javascript.
- **Flux client public** : Seules les informations d’identification de l’utilisateur, collectées par une application, sont envoyées dans l’appel d’API. Les informations d’identification de l’application ne sont pas envoyées.
- **Ajouter de nouvelles revendications** : Il est possible de modifier le contenu du jeton de l’ID pour ajouter de nouvelles revendications. 

Ces flux ne sont pas pris en charge :

- **Serveur à serveur** Le système de protection des identités a besoin d’une adresse IP fiable collectée auprès de l’appelant (le client natif) dans le cadre de l’interaction.  Dans un appel d’API côté serveur, seule l’adresse IP du serveur est utilisée et le système de protection des identités peut identifier une adresse IP répétée en tant que personne malveillante si un seuil dynamique d’échecs d’authentification est dépassé.
- **Flux client confidentiel** : L’ID du client d’application est validé, mais le secret d’application ne l’est pas.

##  <a name="create-a-resource-owner-policy"></a>Créer une stratégie de propriétaire de ressource

1. Connectez-vous au portail Azure en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Pour basculer vers votre client Azure AD B2C, sélectionnez le répertoire B2C dans le coin supérieur droit du portail.
3. Sous **stratégies**, sélectionnez **Stratégies du propriétaire des ressources**.
4. Indiquez un nom pour la stratégie, comme *ROPC_Auth*, puis cliquez sur **Revendications de l’application**.
5. Sélectionnez les revendications de l’application dont vous avez besoin pour votre application, comme *Nom d’affichage*, *Adresse e-mail* et *Fournisseur d’identité*.
6. Cliquez sur **OK**, puis sur **Créer**.

Vous voyez ensuite un point de terminaison comme cet exemple :

`https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Inscrire une application

1. Dans le paramètres B2C, cliquez sur **Applications**, puis sur **+ Ajouter**.
2. Indiquez un nom pour l’application, par exemple *ROPC_Auth_app*.
3. Cliquez sur **Non** pour **Application/API web** et cliquez sur **Oui** pour **Client natif**.
4. Laissez toutes les autres valeurs telles quelles, puis cliquez sur **Créer**.
5. Sélectionnez la nouvelle application et notez l’ID de l’application.

## <a name="test-the-policy"></a>Tester la stratégie

Utilisez votre application de développement d’API favorite pour générer un appel d’API et examinez la réponse pour déboguer votre stratégie. Construisez un appel comme celui-ci, avec les informations contenues dans le tableau suivant en tant que corps de la requête POST :
- Remplacez *yourtenant.onmicrosoft.com* par le nom de votre locataire B2C.
- Remplacez *B2C_1A_ROPC_Auth* par le nom complet de votre stratégie d’informations d’identification par mot de passe du propriétaire de ressource.
- Remplacez *bef2222d56-552f-4a5b-b90a-1988a7d634c3* par l’ID d’application issu de votre inscription.

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Clé | Valeur |
| --- | ----- |
| username | leadiocl@outlook.com |
| password | Passxword1 |
| grant_type | password |
| scope | openid bef2222d56-552f-4a5b-b90a-1988a7d634c3 offline_access |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| response_type | jeton id_token |

*client_id* correspond à la valeur que vous avez notée précédemment en tant qu’ID d’application. *offline_access* est facultatif si vous voulez recevoir un jeton d’actualisation. 

La requête POST réelle ressemble à celle-ci :

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: te.cpim.windows.net
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Une réponse correcte avec accès hors connexion ressemble à l’exemple suivant :

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>Échanger un jeton d’actualisation

Construisez un appel POST comme celui-ci, avec les informations contenues dans le tableau suivant en tant que corps de la requête :

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Clé | Valeur |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| resource | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*client_id* et *ressource* correspondent aux valeurs que vous avez notées précédemment en tant qu’ID d’application. *refresh_token* correspond au jeton que vous avez reçu dans l’appel d’authentification mentionné précédemment.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implémenter avec votre SDK natif préféré ou utiliser l’authentification de l’application


L’implémentation d’Azure AD B2C respecte les normes OAuth 2.0 ou les informations d’identification par mot de passe du propriétaire de ressource du client public et elle doit être compatible avec la plupart des SDK clients.  Nous avons testé ce flux de façon intensive, en production, avec AppAuth pour iOS et AppAuth pour Android.  Consultez https://appauth.io/ pour obtenir les dernières informations.

Vous pouvez télécharger des exemples fonctionnels, configurés pour une utilisation avec Azure AD B2C à partir de github aux adresses https://aka.ms/aadb2cappauthropc pour Android et https://aka.ms/aadb2ciosappauthropc.




