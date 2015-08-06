
Récapitulatif des points important.
====
Code source du cours "Développez votre site web avec le framework Symfony2" sur le site OpenClassrooms    
http://openclassrooms.com/courses/developpez-votre-site-web-avec-le-framework-symfony2   

** Logiciels Nécessaires **
-----
http://msysgit.github.io/  Git + Console d'emulation unix
https://getcomposer.org/download/  Composer, pour l'installation des bundle (modifier le composer.json)


** Vider le cache avec la console **
----

	php app/console cache:clear --env=prod  (pour l'environnement de prod)
	php app/console cache:clear 

** Bundle ** 
----

- Un bundle est une brique de votre application : il contient tout ce qui concerne une fonctionnalité donnée. Cela permet de bien organiser les différentes parties de votre site.
- Il existe des milliers de bundles développés par la communauté, pensez à vérifier qu'il n'existe pas déjà un bundle qui fait ce que vous souhaitez faire ! 

La commande pour générer un nouveau bundle est

	php app/console generate:bundle
	
Un bunble ce nomme :  **ASFolken\NomdubundleBundle**

- Son code source, situé dans src/Application/Bundle, et dont le seul fichier obligatoire est la classe à la racine OCPlatformBundle.php ;
- Enregistrer le bundle dans le noyau pour qu'il soit chargé, en modifiant le fichier app/AppKernel.php ;
- Enregistrer les routes (si le bundle en contient) dans le Routeur pour qu'elles soient chargées, en modifiant le fichier app/config/routing.yml.


** Routeur ** 
----

	oc_platform_home:
	    path:      /{page}
	    defaults:  { _controller: OCPlatformBundle:Advert:index, page: 1 }
	    requirements:
	        page: \d*
	        
	   _format    variable reconnue pour l'extension, charge le bon type de header
	   _locale    variable reconnue pour la langue, charge la bonne translation
	        
- Une route est composée au minimum de deux éléments : l'URL à faire correspondre (son path), et le contrôleur à exécuter (paramètre _controller).
- Le routeur essaie de faire correspondre chaque route à l'URL appelée par l'internaute, et ce dans l'ordre d'apparition des routes : la première route qui correspond est sélectionnée.
- Une route peut contenir des paramètres, facultatifs ou non, représentés par les accolades {paramètre}, et dont la valeur peut être soumise à des contraintes via la section requirements.
- Le routeur est également capable de générer des URL à partir du nom d'une route, et de ses paramètres éventuels.


** Controller ** 
----
http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/Request.html   
http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/Response.html           
http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/JsonResponse.html   

	$url = $this->generateUrl('oc_platform_home');
	$this->render('BundleName:Controller:methode.html.twig', array('key' => $val))          
	$contenu = $this->renderView() recuper le contenu      
	$this->redirect($url);       

-Le rôle du contrôleur est de retourner un objet Response : ceci est obligatoire !
- Le contrôleur construit la réponse en fonction des données qu'il a en entrée : paramètre de route et objet Request.
- Le contrôleur se sert de tout ce dont il a besoin pour construire la réponse : la base de données, les vues, les différents services, etc.	
    

** Twig ** 
------
http://twig.sensiolabs.org/documentation 
http://twig.sensiolabs.org/doc/filters/index.html 

	{% dump(var) %}
	{% set %}
	{{ app.request }} La requête « request » qu'on a vu au chapitre précédent sur les contrôleurs.
	{{ app.session }} Le service « session » qu'on a vu également au chapitre précédent.
	{{ app.environment }} L'environnement courant : « dev », « prod », et ceux que vous avez définis.
	{{ app.debug }} True si le mode debug est activé, False sinon.
	{{ app.security }} Le service « security », que nous verrons plus loin dans ce cours.
	{{ app.user }} L'utilisateur courant, que nous verrons également plus loin dans ce cours.
	href="{{ path('oc_platform_view', { 'id': advert_id }) }}"
	
	{% if var is defined %} … {% endif %}
	{% for valeur, option in liste_options %} ... {% endfor %}
	{% block nom_du_block %}{% endblock %}
	{% extends "OCPlatformBundle::layout.html.twig" %}        quand le template herite d'un autre
	{{ render(controller("OCPlatformBundle:Advert:menu")) }}  pour une partie variable qui depend d'un controller
	{{ include("OCPlatformBundle:Advert:form.html.twig") }}   pour une partie fixe
	
	{{ loop.index }}Le numéro de l'itération courante (en commençant par 1).
	{{ loop.index0 }}Le numéro de l'itération courante (en commençant par 0).
	{{ loop.revindex }} Le nombre d'itérations restantes avant la fin de la boucle (en finissant par 1).
	{{ loop.revindex0 }}Le nombre d'itérations restantes avant la fin de la boucle (en finissant par 0).
	{{ loop.first }}true si c'est la première itération, false sinon.
	{{ loop.last }} true si c'est la dernière itération, false sinon.
	{{ loop.length }} Le nombre total d'itérations dans la boucle.

	{{ var|upper }}
	{{ var|striptags }}
	{{ date|date('d/m/Y') }}
	Date d'aujourd'hui : {{ "now"|date('d/m/Y') }}
	{{ "Il y a %s pommes et %s poires"|format(153, nb_poires) }}
	Longueur de la variable : {{ texte|length }}
	Nombre d'éléments du tableau : {{ tableau|length }}

- Un moteur de templates tel que Twig permet de bien séparer le code PHP du code HTML, dans le cadre de l'architecture MVC ;
- La syntaxe {{ var }}affiche la variable var ;
- La syntaxe {% if %} fait quelque chose, ici une condition ;
- Twig offre un système d'héritage (via {% extends %}) et d'inclusion (via {{ include() }} et {{ render() }}) très intéressant pour bien organiser les templates ;
- Le modèle triple héritage est très utilisé pour des projets avec Symfony2.


** Service **
-----
http://openclassrooms.com/informatique/cours/introduction-a-l-injection-de-dependances-en-php

	$this->get("nom du service")
	$this->container->geet('nomduservice')
	
	services:
		oc_platform.antispam:
			class: OC\PlatformBundle\Antispam\OCAntispa
			arguments: [@mailer, %locale%, 50]

- Un service est une simple classe associée à une certaine configuration.   
- Le conteneur de services organise et instancie tous vos services, grâce à leur configuration.   
- Les services sont la base de Symfony2, et sont très utilisés par le coeur même du framework.   
- L'injection de dépendances est assurée par le conteneur, qui connaît les arguments dont a besoin un service pour fonctionner, et les lui donne donc à sa création.


** Doctrine ORM, Base de données les bases **
----
http://symfony.com/doc/master/book/doctrine.html   
http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/index.html   
http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/basic-mapping.html   
http://www.doctrine-project.org/api/orm/2.1/class-Doctrine.ORM.EntityManager.html   

	php app/console doctrine:database:create      créer la base de données de l'application
	php app/console generate:doctrine:entity      créer une entité 
	php app/console doctrine:generate:entities OCPlatformBundle:Advert    mettre a jour l'entité
	php app/console doctrine:fixtures:load        creer les fixture pour tester l'app
	php app/console doctrine:schema:update --dump-sql      creer le fichier sql d'update la base de donnée
	php app/console doctrine:schema:update --force         applique le fichier sql

3 étapes, Doctrine -> Manager -> Repository    

	$doctrine = $this->getDoctrine(); ou $doctrine = $this->get("doctrine");
	$em = $this->getDoctrine()->getManager();     Pour appeler le Manager d'entité
	$advertRepository = $em->getRepository('OCPlatformBundle:Advert');
	
Exemple d'utilisation	
	
	$em = $this->getDoctrine()->getManager();
	
	// On crée une nouvelle annonce
	$advert1 = new Advert;
	$advert1->setTitle('Recherche développeur.');
	$advert1->setContent("Pour mission courte");
	// Et on le persiste
	$em->persist($advert1);
	
	// On récupère l'annonce d'id 5. On n'a pas encore vu cette méthode find(),
	// mais elle est simple à comprendre. Pas de panique, on la voit en détail
	// dans un prochain chapitre dédié aux repositories
	$advert2 = $em->getRepository('OCPlatformBundle:Advert')->find(5);
	
	// On modifie cette annonce, en changeant la date à la date d'aujourd'hui
	$advert2->setDate(new \Datetime());
	
	// Ici, pas besoin de faire un persist() sur $advert2. En effet, comme on a
	// récupéré cette annonce via Doctrine, il sait déjà qu'il doit gérer cette
	// entité. Rappelez-vous, un persist ne sert qu'à donner la responsabilité
	// de l'objet à Doctrine.
	
	// Enfin, on applique les deux changements à la base de données :
	// Un INSERT INTO pour ajouter $advert1
	// Et un UPDATE pour mettre à jour la date de $advert2
	$em->flush();

- Le rôle d'un ORM est de se charger de la persistance de vos données : vous manipulez des objets, et lui s'occupe de les enregistrer en base de données.    
- L'ORM par défaut livré avec Symfony2 est Doctrine2.    
- L'utilisation d'un ORM implique un changement de raisonnement : on utilise des objets, et on raisonne en POO. C'est au développeur de s'adapter à Doctrine2, et non l'inverse !    
- Une entité est, du point de vue PHP, un simple objet. Du point de vue de Doctrine, c'est un objet complété avec des informations de mapping qui lui permettent d'enregistrer correctement l'objet en base de données.    
- Une entité est, du point de vue de votre code, un objet PHP qui correspond à un besoin, et indépendant du reste de votre application.   
	
**clear($nomEntite)** annule tous les persist() effectués.   

	$em->persist($advert);
	$em->persist($comment);
	$em->clear();
	$em->flush(); // N'exécutera rien, car les deux persists sont annulés par le clear

**detach($entite)** annule le persist() effectué sur l'entité en argument.

	$em->persist($advert);
	$em->persist($comment);
	$em->detach($advert);
	$em->flush(); // Enregistre $comment mais pas $advert

**contains($entite)** retourne true si l'entité donnée en argument est gérée par l'EntityManager      

	$em->persist($advert);
	var_dump($em->contains($advert)); // Affiche true
	var_dump($em->contains($comment)); // Affiche false
 
**refresh($entite)** met à jour l'entité donnée en argument dans l'état où elle est en base de données.       

	$advert->setTitle('Un nouveau titre');
	$em->refresh($advert);
	var_dump($advert->getTitle()); // Affiche « Un ancien titre »
  
**remove($entite)** supprime l'entité donnée en argument de la base de données. Effectif au prochain flush().  

	$em->remove($advert);
	$em->flush(); // Exécute un DELETE sur $advert


** Les différents Types de liaisons **
----
http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/association-mapping.html      
http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/working-with-associations.html      
La notion de propriétaire et d'inverse est abstraite mais importante à comprendre. Dans une relation entre deux entités, il y a toujours une entité dite propriétaire, et une dite inverse.      
- L'entité propriétaire est celle qui contient la référence à l'autre entité. Et dans le cas des relation unidirectionnelle, on ne peut aller que de proprietaire -> inverse      

**Les relations 1-1**

	/**
	* @ORM\OneToOne(targetEntity="OC\PlatformBundle\Entity\Image", cascade={"persist"})   Créer la relation
	* @ORM\JoinColumn(nullable=false)    Rendre la relation obligatoire 
	* */
	private $image;

- On choisit le proprietaire en fonction de la logique de l'application proprietaire -> inverse

**Les relations Many(n)-1**

	 /**
	 * @ORM\ManyToOne(targetEntity="OC\PlatformBundle\Entity\Advert")
	 * @ORM\JoinColumn(nullable=false)
	 */
	 private $advert;

-  C'est le côté Many d'une relation Many-To-One qui est le propriétaire

**Les relations Many(n)-Many(n)**

	class Advert
	{
	  /**
	   * @ORM\ManyToMany(targetEntity="OC\PlatformBundle\Entity\Category", cascade={"persist"})
	   */
	  private $categories;
	}
	
- On choisit le proprietaire en fonction de la logique de l'application proprietaire -> inverse
- L'utilisation du empty de l'ArrayCollection, pour savoir si la liste des catégories est vide ou non ;
- Le {{ advert.categories }} pour récupérer les catégories de l'annonce. C'est exactement la même chose que notre $advert->getCategories() côté PHP ;
- L'utilisation de la variable {{ loop.last }} dans la boucle for pour ne pas mettre de virgule après la dernière catégories affichée.

**Relation Many-To-Many avec attributs**

	Advert One-To-Many AdvertSkill Many-To-One Skill.
	 /**
	   * @ORM\ManyToOne(targetEntity="OC\PlatformBundle\Entity\Advert")
	   * @ORM\JoinColumn(nullable=false)
	   */
	  private $advert;
	
	  /**
	   * @ORM\ManyToOne(targetEntity="OC\PlatformBundle\Entity\Skill")
	   * @ORM\JoinColumn(nullable=false)
	   */
	  private $skill;
  
- Comme les côtés Many des deux relations Many-To-One sont dans AdvertSkill, cette entité est l'entité propriétaire des deux relations.

**Relation Bidirectionnelle**      
Ajouter une relation OneToMany dans l'entité inverse avec un mappedBy

	class Advert
	{
	  /**
	   * @ORM\OneToMany(targetEntity="OC\PlatformBundle\Entity\Application", mappedBy="advert")
	   */
	  private $applications; // Notez le « s », une annonce est liée à plusieurs candidatures
	}
	
Modifier l'entité proprietaire avec un inversedBy      

	class Application
	{
	  /**
	   * @ORM\ManyToOne(targetEntity="OC\PlatformBundle\Entity\Advert", inversedBy="applications")
	   * @ORM\JoinColumn(nullable=false)
	   */
	  private $advert;
	}
	
On modifie un des cotés de la retion pour gérer la dépenace, et on ce sert ensuite de ce setteur pour appelé      

	  public function addApplication(Application $application)
	  {
	    $this->applications[] = $application;
	    // On lie l'annonce à la candidature
	    $application->setAdvert($this);
	    return $this;
	  }

- Les relations Doctrine révèlent toute la puissance de l'ORM.      
- Dans une relation entre deux entités, l'une est propriétaire de la relation et l'autre est inverse. Cette notion est purement technique.      
- Une relation est dite unidirectionnelle si l'entité inverse n'a pas d'attribut la liant à l'entité propriétaire. On met en place une relation bidirectionnelle lorsqu'on a besoin de cet attribut dans l'entité inverse (ce qui arrivera pour certains formulaires, etc.).      

** Récupérer ses entités avec Doctrine2 **      
https://github.com/doctrine/doctrine2/blob/master/lib/Doctrine/ORM/EntityRepository.php      

**La méthode find($id)** récupère tout simplement l'entité correspondant à l'id $id. Dans le cas de notre AdvertRepository, elle retourne une instance d'Advert. Exemple :      

	$repository = $this
	  ->getDoctrine()
	  ->getManager()
	  ->getRepository('OCPlatformBundle:Advert');
	
	$advert = $repository->find(5);

**La méthode findAll()** retourne toutes les entités contenue dans la base de données. Le format du retour est un simple Array, que vous pouvez parcourir (avec un foreach par exemple) pour utiliser les objets qu'il contient.      

	$listAdverts = $repository->findAll();
	
	foreach ($listAdverts as $advert) {
	  // $advert est une instance de Advert
	  echo $advert->getContent();
	}

**La méthode findBy()** est un peu plus intéressante. Comme findAll(), elle permet de retourner une liste d'entités, sauf qu'elle est capable d'effectuer un filtre pour ne retourner que les entités correspondant à un ou plusieurs critère(s)      

	$repository->findBy(
	  array $criteria,
	  array $orderBy = null,
	  $limit  = null,
	  $offset = null

	$listAdverts = $repository->findBy(
	  array('author' => 'Alexandre'), // Critere
	  array('date' => 'desc'),        // Tri
	  5,                              // Limite
	  0                               // Offset
	);

	foreach ($listAdverts as $advert) {
	  // $advert est une instance de Advert
	 echo $advert->getContent();
	}
	
**La méthode findOneBy(array $criteria)** fonctionne sur le même principe que la méthode findBy(), sauf qu'elle ne retourne qu'une seule entité. Les arguments orderBy, limit et offset n'existent donc pas.      

	$advert = $repository->findOneBy(array('author' => 'Marine'));

**findByX($valeur)**, Première méthode, en remplaçant « X » par le nom d'une propriété de votre entité. Dans notre cas, pour l'entité Advert, nous avons donc plusieurs méthodes : findByTitle(), findByDate(), findByAuthor(), findByContent(), etc.

	$listAdverts = $repository->findByAuthor('Alexandre');

**findOneByX($valeur)**

	$advert = $repository->findOneByTitle('Recherche développeur.');

**Les requetes personnelle dans le Repository**      
http://www.doctrine-project.org/docs/orm/2.1/en/reference/query-builder.html

	public function myFindAll()
		{
		  return $this
		    ->createQueryBuilder('a')
		    ->getQuery()
		    ->getResult();
		}

	public function findByAuthorAndDate($author, $year)
	{
	  $qb = $this->createQueryBuilder('a');
	  
	  $qb->where('a.author = :author')
	       ->setParameter('author', $author)
	     ->andWhere('a.date < :year')
	       ->setParameter('year', $year)
	     ->orderBy('a.date', 'DESC')
	  ;
	
	  return $qb
	    ->getQuery()
	    ->getResult()
	  ;
	}

- Le rôle d'un repository est, à l'aide du langage DQL ou du constructeur de requêtes, de récupérer des entités selon des contraintes, des tris, etc.      
- Un repository dispose toujours de quelques méthodes de base, permettant de récupérer de façon très simple les entités.      
- Mais la plupart du temps, il faut créer des méthodes personnelles pour récupérer les entités exactement comme on le veut.      
- Il est indispensable de faire les bonnes jointures afin de limiter au maximum le nombre de requêtes SQL sur vos pages.      


** Les évènements et extensions Doctrine **
------

**Définir l'entité comme contenant des callbacks**      

	/**
	 * @ORM\Entity(repositoryClass="OC\PlatformBundle\Entity\AdvertRepository")
	 * @ORM\HasLifecycleCallbacks()
	 */
	class Advert
	{

**Définir un callback et ses évènements associés**      

	/**
	 * @ORM\PreUpdate
	 */
	public function updateDate()


**PrePersist**      
L'évènement PrePersist se produit juste avant que l'EntityManager ne persiste effectivement l'entité. Concrètement, cela exécute le callback juste avant un $em->persist($entity). Il ne concerne que les entités nouvellement créées. Du coup, il y a deux conséquences : d'une part, les modifications que vous apportez à l'entité seront persistées en base de données, puisqu'elles sont effectives avant que l'EntityManager n'enregistre l'entité en base. D'autre part, vous n'avez pas accès à l'id de l'entité si celui-ci est autogénéré, car justement l'entité n'est pas encore enregistrée en base de données, et donc l'id pas encore généré.      

**PostPersist**      
L'évènement postPersist se produit juste après que l'EntityManager ait effectivement persisté l'entité. Attention, cela n'exécute pas le callback juste après le $em->persist($entity), mais juste après le $em->flush(). À l'inverse du prePersist, les modifications que vous apportez à l'entité ne seront pas persistées en base (mais seront tout de même appliquées à l'entité, attention) ; mais vous avez par contre accès à l'id qui a été généré lors du flush().      

**PreUpdate**      
L'évènement preUpdate se produit juste avant que l'EntityManager ne modifie une entité. Par modifiée, j'entends que l'entité existait déjà, que vous y avez apporté des modifications, puis un $em->flush(). Le callback sera exécuté juste avant le flush(). Attention, il faut que vous ayez modifié au moins un attribut pour que l'EntityManager génère une requête et donc déclenche cet évènement.
Vous avez accès à l'id autogénéré (car l'entité existe déjà), et vos modifications seront persistées en base de données.      

**PostUpdate**      
L'évènement postUpdate se produit juste après que l'EntityManager a effectivement modifié une entité. Vous avez accès à l'id et vos modifications ne sont pas persistées en base de données.      

**PreRemove**      
L'évènement PreRemove se produit juste avant que l'EntityManager ne supprime une entité, c'est-à-dire juste avant un $em->flush() qui précède un $em->remove($entite). Attention, soyez prudents dans cet évènement, si vous souhaitez supprimer des fichiers liés à l'entité par exemple, car à ce moment l'entité n'est pas encore effectivement supprimée, et la suppression peut être annulée en cas d'erreur dans une des opérations à effectuer dans le flush().      

**PostRemove**      
L'évènement PostRemove se produit juste après que l'EntityManager a effectivement supprimé une entité. Si vous n'avez plus accès à son id, c'est ici que vous pouvez effectuer une suppression de fichier associé par exemple.      

**PostLoad**      
L'évènement PostLoad se produit juste après que l'EntityManager a chargé une entité (ou après un $em->refresh()). Utile pour appliquer une action lors du chargement d'une entité.      

- Les évènements permettent de centraliser du code répétitif, afin de systématiser leur exécution et de réduire la duplication de code.
- Plusieurs évènements jalonnent la vie d'une entité, afin de pouvoir exécuter une fonction aux endroits désirés.
- Les extensions permettent de reproduire des comportements communs dans une application, afin d'éviter de réinventer la roue.


** Formulaires **
----------

	php app/console doctrine:generate:form OCPlatformBundle:Advert  creer un formtype

	 // On crée le FormBuilder grâce au service form factory
	    $formBuilder = $this->get('form.factory')->createBuilder('form', $advert);
	    ou
	    $advert = new Advert;
	    $form = $this->get('form.factory')->create(new AdvertType, $advert);
	    $form = $this->createForm(new AdvertType(), $advert).
	    // On ajoute les champs de l'entité que l'on veut à notre formulaire
	    $formBuilder
	      ->add('date',      'date')
	      ->add('title',     'text')
	      ->add('content',   'textarea')
	      ->add('author',    'text')
	      ->add('published', 'checkbox')
	      ->add('save',      'submit')
	    ;
	    // Pour l'instant, pas de candidatures, catégories, etc., on les gérera plus tard
	
	    // À partir du formBuilder, on génère le formulaire
	    $form = $formBuilder->getForm();
	
	    // On passe la méthode createView() du formulaire à la vue
	    // afin qu'elle puisse afficher le formulaire toute seule
	    return $this->render('OCPlatformBundle:Advert:add.html.twig', array(
	      'form' => $form->createView(),
	    ));
	    
http://symfony.com/fr/doc/current/reference/forms/types/      
http://symfony.com/fr/doc/current/book/forms.html#habillage-de-formulaire-theming      
http://symfony.com/fr/doc/current/cookbook/form/create_custom_field_type.html   créer des nouveau type de champs                   
Types de champs    
text
textarea
email
integer
money
number
password
percent
search
url
choice
entity
country
language
locale
timezone
date
datetime
time
birthday
checkbox
file
radio
collection
repeated
hidden
csrf     


** Avec Twig ** 

**form_start()**	
affiche la balise d'ouverture du formulaire HTML, soit <form>. Il faut passer la variable du formulaire en premier argument, et les paramètres en deuxième argument. L'index attr des paramètres, et cela s'appliquera à toutes les fonctions suivantes, représente les attributs à ajouter à la balise générée, ici le <form>. Il nous permet d'appliquer une classe CSS au formulaire, ici form-horizontal.   

**form_errors()**         
affiche les erreurs attachées au champ donné en argument. Nous verrons les erreurs de validation dans le prochain chapitre.	

**form_label()**	
affiche le label HTML du champ donné en argument. Le deuxième argument est le contenu du label.       

**form_widget()**			  
affiche le champ HTML lui-même (que ce soit input, select, etc.)	                     

**form_row()**		
affiche le label, les erreurs et le champ.                        

**form_rest()**	
affiche tous les champs manquants du formulaire (dans notre cas, juste le champ CSRF puisque nous avons déjà affiché à la main tous les autres champs).                   

**form_end()**	
affiche la balise de fermeture du formulaire HTML, soit </form>.	

** Gestion d'evenements de formulaire **

http://symfony.com/doc/current/cookbook/form/dynamic_form_modification.htm		               

	namespace OC\PlatformBundle\Form;
	
	use Symfony\Component\Form\AbstractType;
	use Symfony\Component\Form\FormBuilderInterface;
	// N'oubliez pas ces deux use !
	use Symfony\Component\Form\FormEvent;
	use Symfony\Component\Form\FormEvents;
	use Symfony\Component\OptionsResolver\OptionsResolverInterface;
	
	class AdvertType extends AbstractType
	{
	  public function buildForm(FormBuilderInterface $builder, array $options)
	  {
	    // Ajoutez ici tous vos champs sauf le champ published
	    $builder = ...;
	
	    // On ajoute une fonction qui va écouter un évènement
	    $builder->addEventListener(
	      FormEvents::PRE_SET_DATA,    // 1er argument : L'évènement qui nous intéresse : ici, PRE_SET_DATA
	      function(FormEvent $event) { // 2e argument : La fonction à exécuter lorsque l'évènement est déclenché
	        // On récupère notre objet Advert sous-jacent
	        $advert = $event->getData();
	
	        // Cette condition est importante, on en reparle plus loin
	        if (null === $advert) {
	          return; // On sort de la fonction sans rien faire lorsque $advert vaut null
	        }
	
	        if (!$advert->getPublished() || null === $advert->getId()) {
	          // Si l'annonce n'est pas publiée, ou si elle n'existe pas encore en base (id est null),
	          // alors on ajoute le champ published
	          $event->getForm()->add('published', 'checkbox', array('required' => false));
	        } else {
	          // Sinon, on le supprime
	          $event->getForm()->remove('published');
	        }
	      }
	    );
	  }
	}


- Un formulaire se construit sur un objet existant, et son objectif est d'hydrater cet objet.               
- Un formulaire se construit grâce à un FormBuilder, et dans un fichier XxxType indépendant.                  
- En développement, le rendu d'un formulaire se fait en une seule ligne grâce à la méthode {{ form(form) }}.               
- Il est possible d'imbriquer les formulaires grâce aux XxxType.              
- Le type de champ collection affiche une liste de champs d'un certain type.             
- Le type de champ entity retourne une ou plusieurs entités.                   
- Il est possible d'utiliser le mécanisme d'héritage pour créer des formulaires différents mais ayant la même base.         
- Le type de champ File permet l'upload de fichier, et se couple aux entités grâce aux évènements Doctrine.                  

** Validation de formulaire **

http://symfony.com/fr/doc/current/reference/constraints.html                   

-Le composant validator permet de valider les données d'un objet suivant des règles définies.                
- Cette validation est systématique lors de la soumission d'un formulaire : il est en effet impensable de laisser l'utilisateur entrer ce qu'il veut sans vérifier !                 
- Les règles de validation se définissent via les annotations directement à côté des attributs de la classe à valider. Vous pouvez bien sûr utiliser d'autres formats tels que le YAML ou le XML.                          
- Il est également possible de valider à l'aide de getters, de callbacks ou même de services. Cela rend la procédure de validation très flexible et très puissante.                      

** Sécurité **
--------

- La sécurité se compose de deux couches :                          
   L'authentification, qui définit qui est le visiteur ;                    
   L'autorisation, qui définit si le visiteur a accès à la ressource demandée.                
- Le fichier security.yml permet de configurer finement chaque acteur de la sécurité :                  
   La configuration de l'authentification passe surtout par le paramétrage d'un ou plusieurs pare-feu ;                  
   La configuration de l'autorisation se fait au cas par cas suivant les ressources : on peut sécuriser une méthode de contrôleur, un affichage ou une URL.                   
- Les rôles associés aux utilisateurs définissent les droits dont ils disposent ;                 
- On peut configurer la sécurité pour utiliser FOSUserBundle, un bundle qui offre un espace membres presque clé en main.             
