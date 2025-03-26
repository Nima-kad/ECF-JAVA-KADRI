# ECF-JAVA-KADRI
# Premiere etape:
# Nettoyage des models:
# --------- Niveau 1 Nettoyage ----------- #

- Supprimer tous les imports non utilisés
- Mise en place d’un username unique

# Model utilisateur:

1- J'ai ajouter cet annotation @Column(unique = true, nullable = false) pour assurer que le nom d'utilisateur n'existe pas encore. 
Cependant, chaque fois qu'un utilisateur tente d'ajouter un utilisateur dont le nom d'utilisateur existe déjà dans la base de données, une exception est générée @Column(nullable = false).

2- j'ai supprimé tous les constructeurs, parceque on 'a utilisé ceux générés par @AllArgsConstructor, @NoArgsConstructor.(j'ai chercher sur google) 
lien: https://projectlombok.org/features/constructor

3- j'ai fait un petit refactor sur transactionRecieved en transactionReceived faute orthographe et le nom en français-->anglais 
actif->active et confirmer->confirmed reponse-->response

# UtilisateurService:
-------> Dans confirmerUser()
j'ai remplacer getById(id) par findById(id).get();

		Utilisateur utilisateurToConfirm = utilisateurRepository.getById(id);

		Utilisateur utilisateurToConfirm = utilisateurRepository.findById(id).get();

# transactionService 
Aucun retour ou gestion d'erreur dans createTransaction()
Si le destinataire inexistant, il n’y a aucune alerte côté utilisateur. c pour ça j'ai retourner un boolean true si le destinataire existe sino false 

# Problème d'annulation d'une transaction -> amount négatif???# en thymleleaf
dans Transaction.java 
@Min(value = 0, message = "Le montant doit être positif")

# HomeController
1-  on a ça 
import ch.qos.logback.core.model.Model; 
je l'ai modifier on ça 
import org.springframework.ui.Model;

2- 
j'ai rajouté ce condition pour vérifier si l'utilisateur est déja inscrit ou utilisé:
  if (utilisateurService.getUserByUserName(userName) != null) {
			
            model.addAttribute("error", "Nom d'utilisateur est déjà inscrit");
            return "home";
    }
# LES ROLES 
** Faire en sorte que les droits restreignent l'affichage
	Admin -> accès à home et admin
	User -> accès à tout sauf admin
	Suspendu -> accès à home, login, et une page spécifique pour réactiver le compte
** 

 --> je suis baser sur un menu dynamique adapté au rôle connecté
 J'ai ajouter dans le GetMapping de Homecontroller un currentUser not null ou j'ai  
 
 @GetMapping("/")
	public String home(Model model) {
	    Utilisateur currentUser = utilisateurService.getCurrentUser();
	    if (currentUser != null) {
	        model.addAttribute("userRole", currentUser.getRole())):
	        model.addAttribute("userName", currentUser.getUsername());

	        if ("SUSPENDU".equals(currentUser.getRole())) {
	            return "/reactivation";
	        }
	    }
	    return "home";
	}

 et aprés j'ai ajoute un thymeleaf condition sur le menu:
 exemple: 
 <li class="nav-item" th:if="${userRole == 'USER'} or ${userRole == 'ADMIN'}">
        <a class="nav-link" href="/me/user/">Profil</a>
      </li>
que le role user et admin peuvent voir leurs profil  


# Design & Frontend 
En gros j'ai régler plein probléme en améliorant les templates html



- Design et style avec **Bootstrap 5**
- Tous les formulaires, tableaux et pages utilisateurs sont responsives
- Mise en page propre avec container, card table, form-control.
- Couleurs pour plus de clarté (ex : bouton danger pour suspendre)

# Niveau 2

-->dans transaction.java

	private boolean problemeSignale = false;
 
-->dans transactionService

public void signalerProblemeTransaction(int id) {
	    Transaction transactionToSignale = transactionRepository.findById(id).get();
	    transactionToSignale.setProblemeSignale(true);
	    transactionRepository.save(transactionToSignale);
}

--> dans transactionController

@PostMapping("/signalerProbleme")
	public String signalerTransaction(@RequestParam int id, RedirectAttributes redirectAttributes) {
	    transactionService.signalerProblemeTransaction(id);
	    redirectAttributes.addFlashAttribute("message", "Problème signalé avec succès.");
	    return "transaction";
	    }

 --> dans thyemeleaf
 <span th:if="${!transaction.problemeSignale}">
        <form th:action="@{'/transaction/signalerProbleme?id=' + ${transaction.id}}" method="POST">
          <button type="submit" class="btn btn-warning btn-sm">Signaler un problème</button>
      </form>
  </span>
	

