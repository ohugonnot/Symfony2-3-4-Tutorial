Code source du cours "Développez votre site web avec le framework Symfony2" sur le site OpenClassrooms

http://openclassrooms.com/courses/developpez-votre-site-web-avec-le-framework-symfony2

Récapitulatif des points important.

** Vider le cache avec la console ** \n
php app/console cache:clear --env=prod.

** Bundle **
- Un bundle est une brique de votre application : il contient tout ce qui concerne une fonctionnalité donnée. Cela permet de bien organiser les différentes parties de votre site.
- Il existe des milliers de bundles développés par la communauté, pensez à vérifier qu'il n'existe pas déjà un bundle qui fait ce que vous souhaitez faire !
La commande pour générer un nouveau bundle est 
php app/console generate:bundle
Un bunble ce nomme :  ASFolken\NomdubundleBundle
- Son code source, situé dans src/Application/Bundle, et dont le seul fichier obligatoire est la classe à la racine OCPlatformBundle.php ;
- Enregistrer le bundle dans le noyau pour qu'il soit chargé, en modifiant le fichier app/AppKernel.php ;
- Enregistrer les routes (si le bundle en contient) dans le Routeur pour qu'elles soient chargées, en modifiant le fichier app/config/routing.yml.


** Controller **
http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/Request.html
http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/Response.html
http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/JsonResponse.html

$this->render('BundleName:Controller:methode.html.twig', array('key' => $val))
$contenu = $this->renderView() recuper le contenu
$this->redirect($url);

** Twig **
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

{% if var is defined %} … {% endif %}
{% for valeur, option in liste_options %} ... {% endfor %}
{% block nom_du_block %}{% endblock %}
{% extends "OCPlatformBundle::layout.html.twig" %}        quand le template herite d'un autre
{{ render(controller("OCPlatformBundle:Advert:menu")) }}  pour une partie variable qui depend d'un controller
{{ include("OCPlatformBundle:Advert:form.html.twig") }}   pour une partie fixe

** Service **
http://openclassrooms.com/informatique/cours/introduction-a-l-injection-de-dependances-en-php
- Un service est une simple classe associée à une certaine configuration.
- Le conteneur de services organise et instancie tous vos services, grâce à leur configuration.
- Les services sont la base de Symfony2, et sont très utilisés par le c?œur même du framework.
- L'injection de dépendances est assurée par le conteneur, qui connaît les arguments dont a besoin un service pour fonctionner, et les lui donne donc à sa création.

** Doctrine ORM, Base de données **
http://symfony.com/doc/master/book/doctrine.html
http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/index.html
http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/basic-mapping.html
http://www.doctrine-project.org/api/orm/2.1/class-Doctrine.ORM.EntityManager.html

php app/console doctrine:database:create
php app/console generate:doctrine:entity
php app/console doctrine:generate:entities OCPlatformBundle:Advert
php app/console doctrine:fixtures:load
php app/console doctrine:schema:update --dump-sql
php app/console doctrine:schema:update --force

$doctrine = $this->getDoctrine();
$em = $this->getDoctrine()->getManager();
$advertRepository = $em->getRepository('OCPlatformBundle:Advert');

** Les différents Types de liaisons **
/**
  * @ORM\OneToOne(targetEntity="OC\PlatformBundle\Entity\Image", cascade={"persist"})
  * @ORM\JoinColumn(nullable=false)
  */
private $image;

