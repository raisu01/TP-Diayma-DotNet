# Boutique Diayma

TD C# & Technologies .NET – Visual Studio & Débogage (ESP Dakar, 2026/2027)
Auteur : Samba Sine Ndiaye

## Question 8 – Parcours avant l'affichage des produits

**Méthode :** application lancée avec F5 depuis Visual Studio, avec des points d'arrêt dans `Startup` (ligne 20),
`ProductController` (ligne 15) et `CartSummaryViewComponent` (ligne 12). À chaque arrêt, le namespace, la classe
et la méthode ont été relevés dans la barre de navigation de l'éditeur et dans la fenêtre *Pile des appels*.

**Choix du mode :**
- **Pas à pas détaillé (F11)** : pour entrer dans une méthode de notre propre code.
- **Pas à pas principal (F10)** : pour exécuter une ligne sans entrer dedans (affectations, code du framework).
- **Pas à pas sortant (Maj+F11)** : pour terminer la méthode en cours et remonter à l'appelant.

### Parcours observé

| Ordre | Namespace | Classe | Méthode | Mode utilisé |
| --- | --- | --- | --- | --- |
| 1 | `P2FixAnAppDotNetCode` | `Startup` | `Startup(IConfiguration configuration)` | Point d'arrêt (F5) |
| 2 | `P2FixAnAppDotNetCode.Controllers` | `ProductController` | `ProductController(IProductService productService, ILanguageService languageService)` | Pas à pas principal (F10) |
| 3 | `P2FixAnAppDotNetCode.Controllers` | `ProductController` | `Index()` | Pas à pas détaillé (F11) |
| 4 | `P2FixAnAppDotNetCode.Components` | `CartSummaryViewComponent` | `CartSummaryViewComponent(ICart cart)` | Pas à pas détaillé (F11) |

### Explications

1. **`Startup`** : au démarrage, `Program.Main` construit l'hôte web, qui instancie `Startup`. Le débogueur s'arrête
   sur le point d'arrêt du constructeur.
2. **`ProductController`** : depuis `Startup`, deux *pas à pas principaux* (F10) suffisent. `ConfigureServices` et
   `Configure` sont exécutés par le framework sans arrêt (aucun point d'arrêt à l'intérieur), puis la requête vers la
   page d'accueil (`Product/Index`) crée le contrôleur.
3. **`Index()`** : un *pas à pas détaillé* (F11) fait entrer dans l'action. Elle appelle
   `ProductService.GetAllProducts()`, qui appelle `ProductRepository.GetAllProducts()` : la liste des produits en stock,
   triée par nom.
4. **`CartSummaryViewComponent`** : au rendu de la page, le layout invoque le composant du résumé du panier.
   Un *pas à pas détaillé* (F11) mène à son constructeur. La page s'affiche ensuite avec les produits.

### Difficulté rencontrée

Un *pas à pas* qui sort d'une méthode vers le code du framework provoque l'arrêt du programme
(*Access violation*, code `0xc0000005`). Cela vient de l'incompatibilité du débogueur de Visual Studio 2026 avec
.NET Core 2.0. Contournement : enchaîner les points d'arrêt avec F5 au lieu de sortir des méthodes en pas à pas.
