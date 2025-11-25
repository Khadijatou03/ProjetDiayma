
Boutique Diayma - Application E-commerce ASP.NET Core
Description du Projet
Ce projet est une application de commerce électronique développée avec ASP.NET Core MVC. Il a été créé dans le cadre d'un exercice de mise en conformité et de débogage pour se familiariser avec l'environnement de développement intégré (EDI), le workflow de débogage, la gestion de versions avec Git/GitHub et le déploiement d'applications .NET.

Configuration Requise
•	SDK .NET 10.0 (version mise à jour)
•	Visual Studio 2022 ou VS Code
•	Git et Compte GitHub

1. Informations sur la Solution (Tâches 2 & 3)
 Projets de la Solution
La solution Diayma.sln contient 1 projet principal :
•	Diayma (P2FixAnAppDotNetCode.csproj) - Application web ASP.NET Core MVC
Version SDK .NET
La version du Framework cible utilisée par le projet est : .NET 10.0 (net10.0).
Note : Le projet original a été migré de .NET Core 2.0 vers .NET 10.0 pour être compatible avec un SDK plus récent. Le fichier Diayma.csproj a été mis à jour en conséquence.

2. Bugs Identifiés et Corrigés (Tâche 6)
Deux bugs majeurs ont été identifiés dans le code et les corrections suivantes ont été appliquées :
Bug	Fichier / Méthode	Description du Problème	Solution Appliquée
Bug #1	Cart.cs / GetTotalValue()	Le total du panier ne multipliait pas le prix unitaire par la quantité de chaque produit.	Mise à jour pour inclure la multiplication par la quantité (x.Product.Price * x.Quantity).
Bug #2	Cart.cs / FindProductInCartLines	Risque de NullReferenceException si aucun produit n'était trouvé dans le panier.	Utilisation de l'opérateur Null-Conditional (?.) pour retourner null en toute sécurité si la ligne n'existe pas.
Code de la Correction pour Bug #1
Fichier : P2FixAnAppDotNetCode/Models/Cart.cs (Ligne 66)
C#
public double GetTotalValue()
{
    // Correction: Multiplie le prix par la quantité
    return GetCartLineList().Sum(x => x.Product.Price * x.Quantity);
}

3. Points d'Arrêt pour Débogage (Tâche 6)
Pour l'exploration du code et le débogage, les points d'arrêt ont été placés aux lignes suivantes, juste avant ou sur l'instruction clé :
Fichier	Ligne	Objectif du Point d'Arrêt
CartSummaryViewComponent	12	Débogage du composant affichant le résumé du panier.
ProductController	15	Débogage de l'injection des services et de l'accès aux produits.
OrderController	17	Débogage du traitement des commandes.
CartController	15	Débogage des opérations d'ajout/suppression dans le panier.
Startup	20	Débogage de la configuration initiale des services (Constructeur).
________________________________________
4. Flux d'Exécution : Affichage des Produits (Tâche 5)

### Description du Flux d'Exécution

Le flux détaillé des **namespaces, classes et méthodes** visités avant l'affichage des produits sur l'écran d'accueil du navigateur se déroule comme suit :

#### Phase 1 : Démarrage de l'Application

**1. Namespace : `P2FixAnAppDotNetCode`**  
**Classe : `Program`**  
**Méthode : `Main(string[] args)` (ligne 8)**  
**Mode de débogage : Pas à pas principal (F10)**  
Point d'entrée de l'application. Appelle `BuildWebHost(args).Run()`.

**2. Namespace : `P2FixAnAppDotNetCode`**  
**Classe : `Program`**  
**Méthode : `BuildWebHost(string[] args)` (ligne 13)**  
**Mode de débogage : Pas à pas détaillé (F11)**  
Crée et configure l'hôte web avec `WebHost.CreateDefaultBuilder(args)` et `UseStartup<Startup>()`.

**3. Namespace : `Microsoft.AspNetCore`**  
**Classe : `WebHost`**  
**Méthode : `CreateDefaultBuilder(args)` (ligne 14)**  
**Mode de débogage : Pas à pas sortant (Shift+F11)**  
Configuration par défaut du builder (méthode interne du framework).

**4. Namespace : `P2FixAnAppDotNetCode`**  
**Classe : `Startup`**  
**Méthode : `Startup(IConfiguration configuration)` (ligne 20)**  
**Mode de débogage : Pas à pas principal (F10)**  
**🔴 Point d'arrêt - Constructeur** - Initialise la configuration de l'application.

**5. Namespace : `P2FixAnAppDotNetCode`**  
**Classe : `Startup`**  
**Méthode : `ConfigureServices(IServiceCollection services)` (ligne 26)**  
**Mode de débogage : Pas à pas détaillé (F11)**  
Enregistrement des services dans le conteneur d'injection de dépendances (DI) : `ICart`, `IProductService`, `IProductRepository`, `ILanguageService`, etc.

**6. Namespace : `P2FixAnAppDotNetCode.Models.Repositories`**  
**Classe : `ProductRepository`**  
**Méthode : `ProductRepository()` - Constructeur (ligne 13)**  
**Mode de débogage : Pas à pas détaillé (F11)**  
Constructeur appelé lors de l'enregistrement du service. Initialise la liste `_products` et appelle `GenerateProductData()`.

**7. Namespace : `P2FixAnAppDotNetCode.Models.Repositories`**  
**Classe : `ProductRepository`**  
**Méthode : `GenerateProductData()` (ligne 22)**  
**Mode de débogage : Pas à pas détaillé (F11)**  
Génère et ajoute les 9 produits par défaut à la liste `_products` (XIAOMI Redmi 13C, Micro-ondes, etc.).

**8. Namespace : `P2FixAnAppDotNetCode`**  
**Classe : `Startup`**  
**Méthode : `Configure(IApplicationBuilder app, IWebHostEnvironment env)` (ligne 65)**  
**Mode de débogage : Pas à pas principal (F10)**  
Configuration du pipeline HTTP : `UseStaticFiles()`, `UseRequestLocalization()`, `UseSession()`, `UseRouting()`, `UseEndpoints()` avec la route par défaut `{controller=Product}/{action=Index}/{id?}`.

#### Phase 2 : Requête HTTP et Routage

**9. Namespace : `Microsoft.AspNetCore.Routing`**  
**Classe : `RouteMiddleware` (interne)**  
**Méthode : (Méthodes internes du middleware)**  
**Mode de débogage : Pas à pas sortant (Shift+F11)**  
Le middleware de routage analyse l'URL de la requête HTTP entrante (`/`) et la mappe selon la route par défaut vers `ProductController.Index()`.

**10. Namespace : `P2FixAnAppDotNetCode.Controllers`**  
**Classe : `ProductController`**  
**Méthode : `ProductController(IProductService productService, ILanguageService languageService)` - Constructeur (ligne 15)**  
**Mode de débogage : Pas à pas principal (F10)**  
**🔴 Point d'arrêt - Injection de dépendances** - Le conteneur DI instancie le contrôleur et injecte `IProductService` et `ILanguageService`.

**11. Namespace : `P2FixAnAppDotNetCode.Controllers`**  
**Classe : `ProductController`**  
**Méthode : `Index()` (ligne 19)**  
**Mode de débogage : Pas à pas détaillé (F11)**  
Action du contrôleur qui sera exécutée. Appelle `_productService.GetAllProducts()` pour récupérer la liste des produits.

#### Phase 3 : Récupération des Données

**12. Namespace : `P2FixAnAppDotNetCode.Models.Services`**  
**Classe : `ProductService`**  
**Méthode : `GetAllProducts()` (ligne 24)**  
**Mode de débogage : Pas à pas détaillé (F11)**  
Service métier qui délègue la récupération des produits au repository. Appelle `_productRepository.GetAllProducts().ToList()`.

**13. Namespace : `P2FixAnAppDotNetCode.Models.Repositories`**  
**Classe : `ProductRepository`**  
**Méthode : `GetAllProducts()` (ligne 41)**  
**Mode de débogage : Pas à pas détaillé (F11)**  
Filtre les produits avec `Stock > 0` en utilisant `Where(p => p.Stock > 0)`, les trie par nom avec `OrderBy(p => p.Name)`, puis convertit en liste et retourne un tableau.

**14. Namespace : `System.Linq`**  
**Classe : `Enumerable`**  
**Méthodes : `Where()`, `OrderBy()`, `ToList()` (ligne 43)**  
**Mode de débogage : Pas à pas sortant (Shift+F11)**  
Opérations LINQ sur la liste de produits (méthodes d'extension du framework).

#### Phase 4 : Rendu de la Vue

**15. Namespace : `P2FixAnAppDotNetCode.Controllers`**  
**Classe : `ProductController`**  
**Méthode : `Index()` - Retour (ligne 22)**  
**Mode de débogage : Pas à pas principal (F10)**  
Retourne `View(products)` avec la liste des produits comme modèle.

**16. Namespace : `Microsoft.AspNetCore.Mvc.ViewEngines`**  
**Classe : `ViewEngine` (interne)**  
**Méthode : (Méthodes internes du moteur de vues)**  
**Mode de débogage : Pas à pas sortant (Shift+F11)**  
Le moteur de vues Razor localise et compile la vue `Views/Product/Index.cshtml` en fonction de la culture (en, fr, wo).

**17. Namespace : `P2FixAnAppDotNetCode.Views.Product`**  
**Fichier : `Index.cshtml` (Vue Razor)**  
**Mode de débogage : Pas à pas principal (F10)**  
Vue Razor qui reçoit le modèle `IEnumerable<Product>`. Itère sur les produits avec `@foreach (Product p in Model)` et génère le HTML pour afficher les colonnes : Name, Description, Price, Stock, et les boutons "Ajouter au panier".

**18. Namespace : `P2FixAnAppDotNetCode.Views.Shared`**  
**Fichier : `_Layout.cshtml` (Layout)**  
**Mode de débogage : Pas à pas principal (F10)**  
Layout principal qui enveloppe la vue. Inclut les fichiers CSS, JavaScript, et appelle les composants de vue (`CartSummary`, `LanguageSelector`).

#### Phase 5 : Composants de Vue (ViewComponents)

**19. Namespace : `P2FixAnAppDotNetCode.Components`**  
**Classe : `CartSummaryViewComponent`**  
**Méthode : `CartSummaryViewComponent(ICart cart)` - Constructeur (ligne 12)**  
**Mode de débogage : Pas à pas principal (F10)**  
**🔴 Point d'arrêt - Constructeur** - Injection du panier et conversion en `Cart`.

**20. Namespace : `P2FixAnAppDotNetCode.Components`**  
**Classe : `CartSummaryViewComponent`**  
**Méthode : `Invoke()` (ligne 15)**  
**Mode de débogage : Pas à pas détaillé (F11)**  
Retourne la vue du composant avec le panier pour afficher le résumé (nombre d'articles, total).

**21. Namespace : `P2FixAnAppDotNetCode.Components`**  
**Classe : `LanguageSelectorViewComponent`**  
**Méthode : `Invoke(ILanguageService languageService)` (ligne 8)**  
**Mode de débogage : Pas à pas principal (F10)**  
Retourne la vue du sélecteur de langue pour afficher le dropdown avec les options (Anglais, Français, Espagnol, Wolof).

#### Phase 6 : Réponse HTTP

**22. Namespace : `Microsoft.AspNetCore.Http`**  
**Classe : `HttpResponse` (interne)**  
**Méthode : (Méthodes internes de réponse HTTP)**  
**Mode de débogage : Pas à pas sortant (Shift+F11)**  
Le HTML généré est envoyé comme réponse HTTP au navigateur via les méthodes internes du framework.

### Résumé du Flux Complet

```
Program.Main() (F10)
  ↓
Program.BuildWebHost() (F11)
  ↓
Startup.Constructeur ligne 20 (🔴 Point d'arrêt - F10)
  ↓
Startup.ConfigureServices() (F11)
  ↓
ProductRepository.Constructeur (F11)
  ↓
ProductRepository.GenerateProductData() (F11)
  ↓
Startup.Configure() (F10)
  ↓
[Attente requête HTTP]
  ↓
RouteMiddleware - Routage (Shift+F11)
  ↓
ProductController.Constructeur ligne 15 (🔴 Point d'arrêt - F10)
  ↓
ProductController.Index() (F11)
  ↓
ProductService.GetAllProducts() (F11)
  ↓
ProductRepository.GetAllProducts() (F11)
  ↓
ProductController.Index() retourne View(products) (F10)
  ↓
Views/Product/Index.cshtml - Rendu (F10)
  ↓
Views/Shared/_Layout.cshtml (F10)
  ↓
CartSummaryViewComponent.Constructeur ligne 12 (🔴 Point d'arrêt - F10)
  ↓
CartSummaryViewComponent.Invoke() (F11)
  ↓
LanguageSelectorViewComponent.Invoke() (F10)
  ↓
HTML généré → Navigateur
```

### Modes de Débogage Utilisés

**Pas à pas détaillé (F11)**  
Utilisé pour entrer dans les méthodes et suivre l'exécution complète. Exemples dans ce flux : `ProductService.GetAllProducts()`, `ProductRepository.GetAllProducts()`, `ProductRepository.GenerateProductData()`, `CartSummaryViewComponent.Invoke()`.

**Pas à pas principal (F10)**  
Utilisé pour exécuter ligne par ligne sans entrer dans les appels de méthodes. Exemples dans ce flux : Points d'arrêt aux constructeurs (`Startup` ligne 20, `ProductController` ligne 15, `CartSummaryViewComponent` ligne 12), retours de méthodes, rendu des vues Razor.

**Pas à pas sortant (Shift+F11)**  
Utilisé pour sortir rapidement d'une méthode du framework sans suivre son exécution interne. Exemples dans ce flux : Méthodes internes du framework ASP.NET Core (`WebHost.CreateDefaultBuilder()`, `RouteMiddleware`, `ViewEngine`, `HttpResponse`).

### Points d'Arrêt Stratégiques

Les points d'arrêt ont été placés aux lignes suivantes pour observer le flux :

1. **Startup ligne 20** : Observation de l'initialisation de la configuration lors du démarrage de l'application.
2. **ProductController ligne 15** : Observation de l'injection de dépendances lors de la création du contrôleur.
3. **CartSummaryViewComponent ligne 12** : Observation de l'initialisation du composant de vue lors du rendu du layout.

Ces points permettent de suivre le flux complet depuis le démarrage jusqu'à l'affichage final dans le navigateur.
________________________________________
5. Déploiement (Tâche 7)
La solution a été déployée en un exécutable autonome Windows à l'aide de la commande dotnet publish.

### Commande de Publication
```bash
dotnet publish -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true
```

**Paramètres de la commande :**
- `-c Release` : Compile en configuration optimisée.
- `-r win-x64` : Cible un runtime Windows 64-bit.
- `--self-contained true` : Inclut le runtime .NET, permettant l'exécution sans installation préalable du SDK.
- `-p:PublishSingleFile=true` : Empaquette l'application dans un seul fichier exécutable.

### Emplacement de l'Exécutable

**Fichier exécutable principal :**
```
P2FixAnAppDotNetCode\bin\Release\net10.0\win-x64\publish\Diayma.exe
```

### Fichiers à Inclure dans le Drive

Pour que l'application fonctionne correctement, les fichiers suivants doivent être inclus dans le lien Drive :

**Fichiers essentiels :**
1. **`Diayma.exe`** - Exécutable principal (contient le runtime .NET intégré)
2. **`wwwroot/`** - Dossier complet contenant les fichiers statiques (CSS, JavaScript, images, bibliothèques)
3. **`appsettings.json`** - Fichier de configuration de l'application
4. **`web.config`** - Configuration IIS (optionnel mais recommandé)

**Structure recommandée pour le partage :**
```
Diayma-Release.zip
├── Diayma.exe
├── appsettings.json
├── web.config
└── wwwroot/
    ├── css/
    ├── js/
    ├── images/
    └── lib/
```

### 🔗 Lien de l'Exécutable

L'exécutable est disponible dans le dossier de publication et partagé via :
- Lien Google Drive :https://drive.google.com/file/d/1X0B_ebkQHlwUhqTmYPFx_TXhNpLkiekY/view?usp=drive_link


### Instructions d'Utilisation

1. Téléchargez tous les fichiers depuis le Drive (ou décompressez le ZIP)
2. Placez tous les fichiers dans le même dossier
3. Double-cliquez sur **`Diayma.exe`** pour lancer l'application
4. L'application démarre un serveur web local (généralement sur `http://localhost:6000`)
5. Ouvrez votre navigateur à l'adresse affichée dans la console

**Note :** L'exécutable est autonome et ne nécessite pas l'installation du SDK .NET sur l'ordinateur cible.

6. Fonctionnalités Optionnelles (Tâche 8)
a) Ajout du Support de la Langue Wolof
•	La langue Wolof (wo) a été ajoutée.
•	Les fichiers de ressources Wolof (.wo.resx) ont été créés et traduits.
•	La configuration de localisation dans Startup.cs a été mise à jour pour supporter cette nouvelle culture.
b) Commits Significatifs
Au moins 3 commits significatifs ont été réalisés pour documenter les changements importants :
1.	feat: Upgrade to .NET 10.0 SDK : Mise à jour du framework cible et des dépendances.
2.	fix: Corrected cart total calculation and NullRef issue : Implémentation des deux corrections de bugs (Cart.cs).
3.	feat: Added Wolof (wo) language support and localization : Ajout des ressources et configuration pour la nouvelle langue.
c) Dépôt sur Google Classrooms
•	✅ Le lien vers le dépôt GitHub a été déposé sur Google Classrooms.

## 7. Structure du Projet

### Architecture du Projet

Le projet suit l'architecture MVC (Model-View-Controller) avec une séparation claire des responsabilités :

```
Diayma/
├── Diayma.sln                          # Fichier solution Visual Studio
│
└── P2FixAnAppDotNetCode/               # Projet principal
    ├── Diayma.csproj                   # Fichier de configuration du projet
    ├── Program.cs                      # Point d'entrée de l'application
    ├── Startup.cs                      # Configuration des services et middleware
    │
    ├── Controllers/                    # Contrôleurs MVC
    │   ├── CartController.cs          # Gestion du panier (ajout, suppression)
    │   ├── LanguageController.cs      # Changement de langue
    │   ├── OrderController.cs         # Gestion des commandes
    │   └── ProductController.cs       # Affichage des produits
    │
    ├── Models/                         # Modèles de données
    │   ├── Cart.cs                    # Modèle du panier d'achat
    │   ├── ICart.cs                   # Interface du panier
    │   ├── Order.cs                   # Modèle de commande
    │   ├── Product.cs                 # Modèle de produit
    │   │
    │   ├── Repositories/              # Couche d'accès aux données
    │   │   ├── IOrderRepository.cs
    │   │   ├── IProductRepository.cs
    │   │   ├── OrderRepository.cs
    │   │   └── ProductRepository.cs  # Gestion des produits en mémoire
    │   │
    │   ├── Services/                  # Couche de services métier
    │   │   ├── ILanguageService.cs
    │   │   ├── IOrderService.cs
    │   │   ├── IProductService.cs
    │   │   ├── LanguageService.cs    # Gestion de la localisation
    │   │   ├── OrderService.cs      # Logique métier des commandes
    │   │   └── ProductService.cs    # Logique métier des produits
    │   │
    │   └── ViewModels/               # Modèles de vue
    │       └── LanguageViewModel.cs
    │
    ├── Views/                         # Vues Razor
    │   ├── _ViewImports.cshtml       # Imports globaux
    │   ├── _ViewStart.cshtml         # Configuration de démarrage des vues
    │   │
    │   ├── Cart/
    │   │   └── Index.cshtml          # Vue du panier
    │   │
    │   ├── Home/
    │   │   └── Index.cshtml          # Page d'accueil
    │   │
    │   ├── Order/
    │   │   ├── Completed.cshtml      # Confirmation de commande
    │   │   └── Index.cshtml          # Formulaire de commande
    │   │
    │   ├── Product/
    │   │   └── Index.cshtml          # Liste des produits
    │   │
    │   └── Shared/                   # Vues partagées
    │       ├── _Layout.cshtml        # Layout principal
    │       ├── _ValidationScriptsPartial.cshtml
    │       └── Components/
    │           ├── CartSummary/
    │           │   └── Default.cshtml # Résumé du panier
    │           └── LanguageSelector/
    │               └── Default.cshtml # Sélecteur de langue
    │
    ├── Components/                    # Composants de vue (ViewComponents)
    │   ├── CartSummaryViewComponent.cs
    │   └── LanguageSelectorViewComponent.cs
    │
    ├── Resources/                     # Fichiers de ressources (localisation)
    │   ├── Controllers/
    │   │   └── OrderController.*.resx # Ressources pour OrderController
    │   │
    │   ├── Models/
    │   │   └── ViewModels/
    │   │       ├── LanguageViewModel.*.resx
    │   │       └── Order.*.resx       # Messages de validation
    │   │
    │   └── Views/
    │       ├── Cart/
    │       │   └── Index.*.resx      # Traductions de la vue panier
    │       ├── Order/
    │       │   ├── Completed.*.resx
    │       │   └── Index.*.resx
    │       ├── Product/
    │       │   └── Index.*.resx      # Traductions de la vue produits
    │       └── Shared/
    │           └── Components/
    │               ├── CartSummary/
    │               │   └── Default.*.resx
    │               └── LanguageSelector/
    │                   └── Default.*.resx
    │       # * = .en.resx, .fr.resx, .wo.resx (Anglais, Français, Wolof)
    │
    ├── wwwroot/                      # Fichiers statiques
    │   ├── css/                      # Feuilles de style
    │   ├── js/                       # Scripts JavaScript
    │   ├── images/                   # Images (bannières)
    │   └── lib/                      # Bibliothèques tierces
    │       ├── bootstrap/            # Framework CSS Bootstrap
    │       ├── fontawesome/         # Icônes
    │       ├── jquery/              # Bibliothèque jQuery
    │       ├── jquery-validation/   # Validation de formulaires
    │       └── tether/              # Utilitaires
    │
    ├── Properties/
    │   ├── launchSettings.json      # Configuration de lancement
    │   └── PublishProfiles/         # Profils de publication
    │
    ├── appsettings.json              # Configuration de l'application
    └── appsettings.Development.json  # Configuration de développement
```

