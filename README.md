# ECF-JAVA-KADRI
# Premiere etape:
# Nettoyage des models:

** Model user: ** 

1- J'ai ajouter cet annotation @Column(unique = true, nullable = false) pour assurer que le nom d'utilisateur n'existe pas encore. 
Cependant, chaque fois qu'un utilisateur tente d'ajouter un utilisateur dont le nom d'utilisateur existe déjà dans la base de données, une exception est générée @Column(nullable = false).

2- j'ai supprimé tous les constructeurs, parceque on 'a utilisé ceux générés par @AllArgsConstructor, @NoArgsConstructor.(j'ai chercher sur google) 
https://projectlombok.org/features/constructor



