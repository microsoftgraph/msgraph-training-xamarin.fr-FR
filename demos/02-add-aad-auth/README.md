# <a name="completed-module-add-azure-ad-authentication"></a>Module terminé : ajouter l’authentification Azure AD

La version du projet dans ce répertoire reflète l’exécution du didacticiel via l' [authentification Azure ad](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=3). Si vous utilisez cette version du projet, vous devez effectuer le reste du didacticiel en commençant par obtenir les [données du calendrier](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=4).

> **Remarque :** Nous partons du principe que vous avez déjà inscrit une application dans le portail Azure, comme indiqué dans [enregistrer l’application dans le portail](https://docs.microsoft.com/graph/tutorials/xamarin?tutorial-step=2). Vous devez configurer cette version de l’exemple comme suit :
>
> 1. Renommez `./GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example` le fichier `OAuthSettings.cs`.
> 1. Modifiez le `OAuthSettings.cs` fichier et effectuez les modifications suivantes.
>     1. Remplacez `YOUR_APP_ID_HERE` par l' **ID d’application** que vous avez obtenu à partir du portail Azure.
> 1. Dans le projet **GraphTutorial. iOS** , ouvrez le `Info.plist` fichier.
>    1. Sous l’onglet **avancé** , recherchez la section **types d’URL** . Dans la valeur des **schémas** d’URL `YOUR_APP_ID_HERE` , remplacez par l' **ID d’application** que vous avez obtenu à partir du portail Azure.
> 1. Dans le projet **GraphTutorial. Android** , ouvrez le `Properties/AndroidManifest.xml` fichier.
>     1. Remplacez `YOUR_APP_ID_HERE` par l' **ID d’application** que vous avez obtenu à partir du portail Azure.
