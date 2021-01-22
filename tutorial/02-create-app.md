<!-- markdownlint-disable MD002 MD041 -->

1. Ouvrez Visual Studio et sélectionnez **Créer un projet**.

1. Dans la boîte de dialogue Créer un **projet,** choisissez **Application mobile (Xamarin.Forms),** puis sélectionnez **Suivant**.

    ![Visual Studio 2019, boîte de dialogue Créer un projet](images/new-project-dialog.png)

1. Dans la boîte de dialogue Configurer **un nouveau projet,** entrez le nom du projet et le nom de la `GraphTutorial` **solution,** puis sélectionnez **Créer.** 

    > [!IMPORTANT]
    > Veillez à entrer exactement le même nom pour Visual Studio projet spécifié dans ces instructions d’atelier. Le nom du projet Visual Studio fait alors partie de l’espace de noms dans le code. Le code figurant dans ces instructions dépend de l’espace de noms qui correspond au nom de projet Visual Studio spécifié dans celles-ci. Si vous utilisez un autre nom de projet, celui-ci n’est pas compilé sauf si vous ajustez tous les espaces de noms pour qu’ils correspondent au nom de projet Visual Studio saisi à la création du projet.

    ![Visual Studio 2019 configurer la boîte de dialogue nouveau projet](images/configure-new-project-dialog.png)

1. Dans la boîte de dialogue  Nouvelle **application plateforme,** sélectionnez le modèle Vide, puis sélectionnez les plateformes que vous souhaitez créer sous **Plateformes.** Sélectionnez **OK** pour créer la solution.

    ![Visual Studio 2019 nouvelle boîte de dialogue d’application plateforme](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a>Installer des packages

Avant de passer à autre chose, installez des packages NuGet supplémentaires que vous utiliserez ultérieurement.

- [Microsoft.Identity.Client pour gérer](https://www.nuget.org/packages/Microsoft.Identity.Client/) l’authentification Azure AD et la gestion des jetons.
- [Microsoft.Graph pour](https://www.nuget.org/packages/Microsoft.Graph/) effectuer des appels à Microsoft Graph.
- [TimeZoneConverter pour](https://www.nuget.org/packages/TimeZoneConverter/) la gestion des fuseaux horaires sur plusieurs plateformes.

1. Sélectionnez **Outils > Gestionnaire de package NuGet > Console Gestionnaire de package**.

1. Dans le menu Console du Gestionnaire de package, saisissez les commandes suivantes.

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.24.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.21.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Version 3.3.0 -Project GraphTutorial
    ```

## <a name="design-the-app"></a>Concevoir l’application

Commencez par mettre à jour la classe pour ajouter des variables pour suivre l’état `App` d’authentification et l’utilisateur qui est inscrit.

1. Dans **l’Explorateur** de solutions, développez **le projet GraphTutorial,** puis développez le fichier **App.xaml.** Ouvrez **App.xaml.cs** fichier et ajoutez les `using` instructions suivantes en haut du fichier.

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. Ajoutez `INotifyPropertyChanged` l’interface à la déclaration de classe.

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. Ajoutez les propriétés suivantes à la `App` classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. Ajoutez les fonctions suivantes à la `App` classe. Les `SignIn` fonctions et les fonctions ne sont que des `SignOut` `GetUserInfo` espaces réservé pour l’instant.

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

1. La `GetUserPhoto` fonction renvoie une photo par défaut pour l’instant. Vous pouvez fournir votre propre fichier ou télécharger celui utilisé dans l’exemple à partir [de GitHub.](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png) Si vous utilisez votre propre fichier, renommez-le **no-profile-pic.png**.

1. Copiez le fichier dans le répertoire **./GraphTutorial/GraphTutorial.**

1. Cliquez avec le bouton droit sur le fichier dans **l’Explorateur de** solutions et sélectionnez **Propriétés.** Dans la **fenêtre Propriétés,** modifiez la valeur de **l’action** de build en **ressource incorporée.**

    ![Capture d’écran de la fenêtre Propriétés du fichier PNG](./images/png-file-properties.png)

### <a name="app-navigation"></a>Navigation dans l’application

Dans cette section, vous allez modifier la page principale de l’application en [page Maître/Détails.](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page) Cela fournit un menu de navigation pour basculer d’une vue à l’autre dans l’application.

1. Ouvrez **le fichier MainPage.xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml" id="MainPageXamlSnippet":::

#### <a name="implement-the-menu"></a>Implémenter le menu

1. Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouveau dossier.** Nommer le dossier `Models`.

1. Cliquez avec le bouton droit sur le dossier **Modèles** et **sélectionnez Ajouter,** puis **Classe...**. Nommez la classe `NavMenuItem` et sélectionnez **Ajouter.**

1. Ouvrez **NavMenuItem.cs** fichier et remplacez son contenu par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouvel élément...**. Choose **Content Page** and name the page `MenuPage` . Sélectionnez **Ajouter**.

1. Ouvrez **le fichier MenuPage.xaml** et remplacez son contenu par ce qui suit.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml" id="MenuPageXamlSnippet":::

1. Développez **MenuPage.xaml dans** l’Explorateur de solutions et ouvrez **MenuPage.xaml.cs** fichier.  Remplacez son contenu par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > Visual Studio signalera des erreurs **dans MenuPage.xaml.cs**. Ces erreurs seront résolues dans une étape ultérieure.

#### <a name="implement-the-welcome-page"></a>Implémenter la page d’accueil

1. Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouvel élément...**. Choose **Content Page** and name the page `WelcomePage` . Sélectionnez **Ajouter**. Ouvrez **le fichier WelcomePage.xaml** et remplacez son contenu par ce qui suit.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml" id="WelcomePageXamlSnippet":::

1. Développez **WelcomePage.xaml dans** **l’Explorateur** de solutions et ouvrez **WelcomePage.xaml.cs** fichier. Ajoutez la fonction suivante à la classe `WelcomePage`.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a>Ajouter un calendrier et de nouvelles pages d’événements

Maintenant, ajoutez une page de calendrier et une nouvelle page d’événements. Ces espaces seront simplement des espaces réservé pour le moment.

1. Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouvel élément...**. Choose **Content Page** and name the page `CalendarPage` . Sélectionnez **Ajouter**.

1. Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouvel élément...**. Choose **Content Page** and name the page `NewEventPage` . Sélectionnez **Ajouter**.

#### <a name="update-mainpage-code-behind"></a>Mettre à jour le code-behind MainPage

Maintenant que toutes les pages sont en place, mettez à jour le code-behind **pour MainPage.xaml**.

1. Développez **MainPage.xaml** dans l’Explorateur de solutions et ouvrez **MainPage.xaml.cs** fichier et remplacez tout son contenu par ce qui suit. 

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. Enregistrez toutes vos modifications. Cliquez avec le bouton droit sur le projet que vous souhaitez exécuter (Android, iOS ou UWP) et sélectionnez Définir comme **projet de** démarrage. Appuyez **sur F5** ou **sélectionnez Déboguer > démarrer le débogage** dans Visual Studio.

    ![Captures d’écran des versions Android, iOS et UWP de l’application](./images/welcome-page.png)
