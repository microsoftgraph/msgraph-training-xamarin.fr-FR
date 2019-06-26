<!-- markdownlint-disable MD002 MD041 -->

Ouvrez Visual Studio, puis sélectionnez **créer un nouveau projet**. Dans la boîte de dialogue **créer un nouveau projet** , choisissez **application mobile (Xamarin. Forms)**, puis cliquez sur **suivant**.

![Boîte de dialogue créer un nouveau projet dans Visual Studio 2019](images/new-project-dialog.png)

Dans la boîte de dialogue **configurer un nouveau projet** , entrez `GraphTutorial` le **nom du projet** et le nom de la **solution**, puis sélectionnez **créer**.

> [!IMPORTANT]
> Assurez-vous d’entrer exactement le même nom pour le projet Visual Studio spécifié dans ces instructions d’atelier. Le nom du projet Visual Studio devient partie intégrante de l’espace de noms dans le code. Le code à l’intérieur de ces instructions dépend de l’espace de noms correspondant au nom de projet Visual Studio spécifié dans ces instructions. Si vous utilisez un nom de projet différent, le code n’est pas compilé, sauf si vous ajustez tous les espaces de noms pour qu’ils correspondent au nom de projet Visual Studio que vous entrez lors de la création du projet.

![Boîte de dialogue Configurer un nouveau projet de Visual Studio 2019](images/configure-new-project-dialog.png)

Dans la boîte de dialogue **nouvelle application** multiplateforme, sélectionnez le modèle **vierge** , puis sélectionnez les plateformes que vous souhaitez **** créer sous plateformes. Sélectionnez **OK** pour créer la solution.

![Nouvelle boîte de dialogue d’application multiplateforme Visual Studio 2019](images/new-cross-platform-app-dialog.png)

Avant de poursuivre, installez des packages NuGet supplémentaires que vous utiliserez plus tard.

- [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client/) pour gérer l’authentification Azure ad et la gestion des jetons.
- [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) pour effectuer des appels à Microsoft Graph.

Sélectionnez **outils > gestionnaire de package NuGet > console Gestionnaire de package**. Dans la console Gestionnaire de package, entrez les commandes suivantes.

```Powershell
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial.iOS
Install-Package Microsoft.Graph -Version 1.15.0 -Project GraphTutorial
```

## <a name="design-the-app"></a>Concevoir l’application

Commencez par mettre à jour `App` la classe pour ajouter des variables afin de suivre l’état d’authentification et l’utilisateur connecté. Dans l' **Explorateur de solutions**, développez le projet **GraphTutorial** , puis développez le fichier **app. Xaml** . Ouvrez le fichier **app.Xaml.cs** et ajoutez les instructions `using` suivantes en haut du fichier.

```cs
using System.ComponentModel;
using System.IO;
using System.Reflection;
using System.Threading.Tasks;
```

Ensuite, ajoutez l' `INotifyPropertyChanged` interface à la déclaration de classe.

```cs
public partial class App : Application, INotifyPropertyChanged
```

À présent, ajoutez les propriétés suivantes `App` à la classe.

```cs
// Is a user signed in?
private bool isSignedIn;
public bool IsSignedIn
{
    get { return isSignedIn; }
    set
    {
        isSignedIn = value;
        OnPropertyChanged("IsSignedIn");
        OnPropertyChanged("IsSignedOut");
    }
}

public bool IsSignedOut { get { return !isSignedIn; } }

// The user's display name
private string userName;
public string UserName
{
    get { return userName; }
    set
    {
        userName = value;
        OnPropertyChanged("UserName");
    }
}

// The user's email address
private string userEmail;
public string UserEmail
{
    get { return userEmail; }
    set
    {
        userEmail = value;
        OnPropertyChanged("UserEmail");
    }
}

// The user's profile photo
private ImageSource userPhoto;
public ImageSource UserPhoto
{
    get { return userPhoto; }
    set
    {
        userPhoto = value;
        OnPropertyChanged("UserPhoto");
    }
}
```

À présent, ajoutez les fonctions suivantes `App` à la classe. Les `SignIn`fonctions `SignOut`, et `GetUserInfo` sont simplement des espaces réservés pour l’instant.

```cs
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

La `GetUserPhoto` fonction renvoie une photo par défaut pour le moment. Vous pouvez soit fournir votre propre fichier ici, soit télécharger celui utilisé dans l’exemple à partir de [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png). Copiez le fichier dans `./GraphTutorial/GraphTutorial` le répertoire. Cliquez avec le bouton droit sur le projet **GraphTutorial** dans l' **Explorateur de solutions** et sélectionnez **Ajouter**, puis **élément existant...**. Sélectionnez le `no-profile-pic.png` fichier et sélectionnez **Ajouter**. À présent, cliquez avec le bouton droit sur le fichier dans l' **Explorateur de solutions** , puis sélectionnez **Propriétés**. Dans la fenêtre **Propriétés** , modifiez l’action de **génération** en **ressource incorporée**.

![Capture d’écran de la fenêtre Propriétés pour le fichier PNG](./images/png-file-properties.png)

### <a name="app-navigation"></a>Navigation de l’application

Ensuite, définissez la page principale de l’application sur une [page maître/détails](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page). Cela permet de fournir un menu de navigation permettant de basculer entre l’affichage dans l’application.

Ouvrez le fichier **MainPage. Xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:GraphTutorial"
             x:Class="GraphTutorial.MainPage">

    <MasterDetailPage.Master>
        <local:MenuPage/>
    </MasterDetailPage.Master>

    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:WelcomePage/>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>

</MasterDetailPage>
```

#### <a name="implement-the-menu"></a>Implémenter le menu

Commencez par créer un modèle pour représenter les éléments de menu. Cliquez avec le bouton droit sur le projet **GraphTutorial** et sélectionnez **Ajouter**, puis **nouveau dossier**. Nommez le `Models`dossier.

Cliquez avec le bouton droit sur le dossier **modèles** , sélectionnez **Ajouter**, puis **classe..**.. Nommez la `NavMenuItem` classe et sélectionnez **Ajouter**. Ouvrez le fichier **NavMenuItem.cs** et remplacez son contenu par ce qui suit.

```cs
namespace GraphTutorial.Models
{
    public enum MenuItemType
    {
        Welcome,
        Calendar
    }

    public class NavMenuItem
    {
        public MenuItemType Id { get; set; }

        public string Title { get; set; }
    }
}
```

À présent, ajoutez la page de menu. Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `MenuPage`la page. Cliquez sur **Ajouter**. Ouvrez le fichier **MenuPage. Xaml** et remplacez son contenu par ce qui suit.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.UseSafeArea="true"
             Title="Menu"
             x:Class="GraphTutorial.MenuPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout VerticalOptions="Start" HorizontalOptions="Center">
            <StackLayout x:Name="UserArea" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Sign in to get started"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Medium"
                       Margin="10,20,10,20" />
                <Button Text="Sign in"
                        Clicked="OnSignIn"
                        HorizontalOptions="Center" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Image Source="{Binding Path=UserPhoto, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       Margin="0,20,0,10" />
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Small" />
                <Label Text="{Binding Path=UserEmail, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Italic" />
                <Button Text="Sign out"
                        Margin="0,20,0,20"
                        Clicked="OnSignOut"
                        HorizontalOptions="Center" />
                <ListView x:Name="ListViewMenu"
                          HasUnevenRows="True"
                          HorizontalOptions="Start">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <ViewCell>
                                <Grid Padding="10">
                                    <Label Text="{Binding Title}" FontSize="20"/>
                                </Grid>
                            </ViewCell>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Maintenant, développez **MenuPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MenuPage.Xaml.cs** . Remplacez son contenu par ce qui suit.

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

using GraphTutorial.Models;

namespace GraphTutorial
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MenuPage : ContentPage
    {
        MainPage RootPage => Application.Current.MainPage as MainPage;
        List<NavMenuItem> menuItems;

        public MenuPage ()
        {
            InitializeComponent ();

            // Add items to the menu
            menuItems = new List<NavMenuItem>
            {
                new NavMenuItem {Id = MenuItemType.Welcome, Title="Home" },
                new NavMenuItem {Id = MenuItemType.Calendar, Title="Calendar" }
            };
            ListViewMenu.ItemsSource = menuItems;

            // Initialize the selected item
            ListViewMenu.SelectedItem = menuItems[0];

            // Handle the ItemSelected event to navigate to the
            // selected page
            ListViewMenu.ItemSelected += async (sender, e) =>
            {
                if (e.SelectedItem == null)
                    return;

                var id = (int)((NavMenuItem)e.SelectedItem).Id;
                await RootPage.NavigateFromMenu(id);
            };
        }

        private async void OnSignOut(object sender, EventArgs e)
        {
            var signout = await DisplayAlert("Sign out?", "Do you want to sign out?", "Yes", "No");
            if (signout)
            {
                await (Application.Current as App).SignOut();
            }
        }

        private async void OnSignIn(object sender, EventArgs e)
        {
            await (Application.Current as App).SignIn();
        }
    }
}
```

> [!NOTE]
> Visual Studio signale les erreurs dans **MenuPage.Xaml.cs**. Ces erreurs seront résolues au cours d’une étape ultérieure.

#### <a name="implement-the-welcome-page"></a>Implémenter la page d’accueil

Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `WelcomePage`la page. Cliquez sur **Ajouter**. Ouvrez le fichier **WelcomePage. Xaml** et remplacez son contenu par ce qui suit.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             Title="Home"
             x:Class="GraphTutorial.WelcomePage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Graph Xamarin Tutorial App"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Large"
                       Margin="10,20,10,20" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Please sign in to get started"
                       HorizontalOptions="Center"
                       FontSize="Medium"
                       Margin="10,0,10,20"/>
                <Button Text="Sign in"
                        HorizontalOptions="Center"
                        Clicked="OnSignIn" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}, StringFormat='Welcome \{0\}!'}"
                       HorizontalOptions="Center"
                       FontSize="Medium"/>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Maintenant, développez **WelcomePage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **WelcomePage.Xaml.cs** . Ajoutez la fonction suivante à la classe `WelcomePage`.

```cs
private void OnSignIn(object sender, EventArgs e)
{
    (App.Current.MainPage as MasterDetailPage).IsPresented = true;
}
```

#### <a name="add-calendar-page"></a>Ajouter une page de calendrier

Maintenant, ajoutez une page de calendrier. Il s’agit uniquement d’un espace réservé pour l’instant. Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `CalendarPage`la page. Cliquez sur **Ajouter**.

Laissez la page ajoutée telle quelle pour le moment.

#### <a name="update-mainpage-code-behind"></a>Mettre à jour le code MainPage-behind

Maintenant que toutes les pages sont en place, mettez à jour le code-behind pour **MainPage. Xaml**. Développez **MainPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MainPage.Xaml.cs** et remplacez l’intégralité de son contenu par ce qui suit.

```cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Xamarin.Forms;
using GraphTutorial.Models;

namespace GraphTutorial
{
    public partial class MainPage : MasterDetailPage
    {
        Dictionary<int, NavigationPage> MenuPages = new Dictionary<int, NavigationPage>();

        public MainPage()
        {
            InitializeComponent();

            MasterBehavior = MasterBehavior.Popover;

            // Load the welcome page at start
            MenuPages.Add((int)MenuItemType.Welcome, (NavigationPage)Detail);
        }

        // Navigate to the selected page
        public async Task NavigateFromMenu(int id)
        {
            if (!MenuPages.ContainsKey(id))
            {
                switch (id)
                {
                    case (int)MenuItemType.Welcome:
                        MenuPages.Add(id, new NavigationPage(new WelcomePage()));
                        break;
                    case (int)MenuItemType.Calendar:
                        MenuPages.Add(id, new NavigationPage(new CalendarPage()));
                        break;
                }
            }

            var newPage = MenuPages[id];

            if (newPage != null && Detail != newPage)
            {
                Detail = newPage;

                if (Device.RuntimePlatform == Device.Android)
                    await Task.Delay(100);

                IsPresented = false;
            }
        }
    }
}
```

Enregistrez toutes vos modifications. Cliquez avec le bouton droit sur le projet que vous souhaitez exécuter (Android, iOS ou UWP) et sélectionnez **définir comme projet de démarrage**. Appuyez sur **F5** ou sélectionnez déboguer **> démarrer** le débogage dans Visual Studio.

![Captures d’écran des versions Android, iOS et UWP de l’application](./images/welcome-page.png)
