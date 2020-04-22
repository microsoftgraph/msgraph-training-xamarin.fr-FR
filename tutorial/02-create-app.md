<!-- markdownlint-disable MD002 MD041 -->

1. Ouvrez Visual Studio et sélectionnez **Créer un projet**.

1. Dans la boîte de dialogue **créer un nouveau projet** , choisissez **application mobile (Xamarin. Forms)**, puis cliquez sur **suivant**.

    ![Boîte de dialogue créer un nouveau projet dans Visual Studio 2019](images/new-project-dialog.png)

1. Dans la boîte de dialogue **configurer un nouveau projet** , entrez `GraphTutorial` le **nom du projet** et le nom de la **solution**, puis sélectionnez **créer**.

    > [!IMPORTANT]
    > Assurez-vous d’entrer exactement le même nom pour le projet Visual Studio spécifié dans ces instructions d’atelier. Le nom du projet Visual Studio fait alors partie de l’espace de noms dans le code. Le code figurant dans ces instructions dépend de l’espace de noms qui correspond au nom de projet Visual Studio spécifié dans celles-ci. Si vous utilisez un autre nom de projet, celui-ci n’est pas compilé sauf si vous ajustez tous les espaces de noms pour qu’ils correspondent au nom de projet Visual Studio saisi à la création du projet.

    ![Boîte de dialogue Configurer un nouveau projet de Visual Studio 2019](images/configure-new-project-dialog.png)

1. Dans la boîte de dialogue **nouvelle application** multiplateforme, sélectionnez le modèle **vierge** , puis sélectionnez les plateformes que vous souhaitez créer sous **plateformes**. Sélectionnez **OK** pour créer la solution.

    ![Nouvelle boîte de dialogue d’application multiplateforme Visual Studio 2019](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a>Installer des packages

Avant de poursuivre, installez des packages NuGet supplémentaires que vous utiliserez plus tard.

- [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client/) pour gérer l’authentification Azure ad et la gestion des jetons.
- [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) pour effectuer des appels à Microsoft Graph.

1. Sélectionnez **Outils > Gestionnaire de package NuGet > Console Gestionnaire de package**.

1. Dans le menu Console du Gestionnaire de package, saisissez les commandes suivantes.

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.0.1 -Project GraphTutorial
    ```

## <a name="design-the-app"></a>Concevoir l’application

Commencez par mettre à jour `App` la classe pour ajouter des variables afin de suivre l’état d’authentification et l’utilisateur connecté.

1. Dans l' **Explorateur de solutions**, développez le projet **GraphTutorial** , puis développez le fichier **app. Xaml** . Ouvrez le fichier **app.Xaml.cs** et ajoutez les instructions `using` suivantes en haut du fichier.

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. Ajoutez l' `INotifyPropertyChanged` interface à la déclaration de classe.

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. Ajoutez les propriétés suivantes à la `App` classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. Ajoutez les fonctions suivantes à la `App` classe. Les `SignIn`fonctions `SignOut`, et `GetUserInfo` sont simplement des espaces réservés pour l’instant.

    ```csharp
    public async Task SignIn()
    {
        await GetUserInfo();

        IsSignedIn = true;
    }

    public async Task SignOut()
    {
        UserPhoto = null;
        UserName = string.Empty;
        UserEmail = string.Empty;
        IsSignedIn = false;
    }

    private async Task GetUserInfo()
    {
        UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
        UserName = "Adele Vance";
        UserEmail = "adelev@contoso.com";
    }

    private Stream GetUserPhoto()
    {
        // Return the default photo
        return Assembly.GetExecutingAssembly().GetManifestResourceStream("GraphTutorial.no-profile-pic.png");
    }
    ```

1. La `GetUserPhoto` fonction renvoie une photo par défaut pour le moment. Vous pouvez fournir votre propre fichier ou télécharger celui utilisé dans l’exemple à partir de [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png). Si vous utilisez votre propre fichier, renommez-le en **no-Profile-pic. png**.

1. Copiez le fichier dans le répertoire **./GraphTutorial/GraphTutorial** .

1. Cliquez avec le bouton droit sur le fichier dans l' **Explorateur de solutions** , puis sélectionnez **Propriétés**. Dans la fenêtre **Propriétés** , modifiez l’action de **génération** en **ressource incorporée**.

    ![Capture d’écran de la fenêtre Propriétés pour le fichier PNG](./images/png-file-properties.png)

### <a name="app-navigation"></a>Navigation de l’application

Dans cette section, vous allez remplacer la page principale de l’application par une [page maître/détails](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page). Cela permet de fournir un menu de navigation permettant de basculer entre l’affichage dans l’application.

1. Ouvrez le fichier **MainPage. Xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a>Implémenter le menu

1. Cliquez avec le bouton droit sur le projet **GraphTutorial** et sélectionnez **Ajouter**, puis **nouveau dossier**. Nommer le dossier `Models`.

1. Cliquez avec le bouton droit sur le dossier **modèles** , sélectionnez **Ajouter**, puis **classe..**.. Nommez la `NavMenuItem` classe et sélectionnez **Ajouter**.

1. Ouvrez le fichier **NavMenuItem.cs** et remplacez son contenu par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `MenuPage`la page. Sélectionnez **Ajouter**.

1. Ouvrez le fichier **MenuPage. Xaml** et remplacez son contenu par ce qui suit.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. Développez **MenuPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MenuPage.Xaml.cs** . Remplacez son contenu par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > Visual Studio signale les erreurs dans **MenuPage.Xaml.cs**. Ces erreurs seront résolues au cours d’une étape ultérieure.

#### <a name="implement-the-welcome-page"></a>Implémenter la page d’accueil

1. Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `WelcomePage`la page. Sélectionnez **Ajouter**. Ouvrez le fichier **WelcomePage. Xaml** et remplacez son contenu par ce qui suit.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. Développez **WelcomePage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **WelcomePage.Xaml.cs** . Ajoutez la fonction suivante à la classe `WelcomePage`.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-page"></a>Ajouter une page de calendrier

Maintenant, ajoutez une page de calendrier. Il s’agit uniquement d’un espace réservé pour l’instant.

1. Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `CalendarPage`la page. Sélectionnez **Ajouter**.

#### <a name="update-mainpage-code-behind"></a>Mettre à jour le code MainPage-behind

Maintenant que toutes les pages sont en place, mettez à jour le code-behind pour **MainPage. Xaml**.

1. Développez **MainPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MainPage.Xaml.cs** et remplacez l’intégralité de son contenu par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. Enregistrez toutes vos modifications. Cliquez avec le bouton droit sur le projet que vous souhaitez exécuter (Android, iOS ou UWP) et sélectionnez **définir comme projet de démarrage**. Appuyez sur **F5** ou sélectionnez **déboguer > démarrer le débogage** dans Visual Studio.

    ![Captures d’écran des versions Android, iOS et UWP de l’application](./images/welcome-page.png)
