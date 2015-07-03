---
ID: 411
post_title: >
  Premiers pas avec ElasticSearch (Partie
  1)
author: tleroux
post_date: 2012-11-14 09:05:00
post_excerpt: |
  <p><strong><a href="http://www.elasticsearch.org/">ElasticSearch</a></strong> est un moteur de recherche open source qui fait beaucoup parler de lui. Et pour cause, il possède un atout majeur&nbsp;: il suffit de quelques minutes à peine pour disposer d'un moteur de recherche clusterisé, automatiquement sauvegardé et répliqué, interrogeable via une API REST et proposant toutes les fonctionnalités d'un moteur de recherche dernière génération.</p>
layout: post
permalink: http://blog.zenika-offres.com/?p=411
published: true
slide_template:
  - ""
---
<p><strong><a href="http://www.elasticsearch.org/">ElasticSearch</a></strong> est un moteur de recherche open source qui fait beaucoup parler de lui. Et pour cause, il possède un atout majeur&nbsp;: il suffit de quelques minutes à peine pour disposer d'un moteur de recherche clusterisé, automatiquement sauvegardé et répliqué, interrogeable via une API REST et proposant toutes les fonctionnalités d'un moteur de recherche dernière génération.</p>
<!--more-->
<p>Malgré une prise en main rapide et une documentation officielle très complète, l'utilisation d'ElasticSearch peut devenir rapidement complexe pour qui n'a jamais utilisé de moteur de recherche. C'est pourquoi nous avons choisi de démarrer une nouvelle série d'articles, dans laquelle nous allons essayer de présenter les notions de base d'ElasticSearch et les fonctionnalités les plus utilisées de ce fantastique outil.</p> <p>Dans ce premier article, nous verrons comment installer et configurer ElasticSearch. Nous en profiterons pour introduire les concepts de nœud et de cluster, puis nous effectuerons nos premières indexations de documents et recherches par mots clés.</p> <h4>ElasticSearch&nbsp;: cool, bonsaï, cool&nbsp;!</h4> <p><a href="http://www.elasticsearch.org/">ElasticSearch</a> est un projet open source développé en Java sous <a href="http://www.apache.org/licenses/LICENSE-2.0.html">licence Apache2</a>. Le projet a été présenté par son créateur, <a href="http://www.kimchy.org/">Shay Banon</a>, comme le successeur du framework Compass (un framework de mapping objet / moteur de recherche partageant des similitudes avec <a href="http://www.hibernate.org/subprojects/search.html">Hibernate Search</a>).</p> <p>La première version a été mise à disposition du public en février 2010. Depuis, le projet tient un rythme soutenu de releases à raison d'une version tous les un ou deux mois.</p> <p>La version actuelle est 0.19.11, mais ne vous fiez pas à ce numéro! Le projet est largement mature et <a href="http://www.elasticsearch.org/users/">est déjà utilisé en production</a> par des organisations importantes telles que la Fondation Mozilla, <a href="http://engineering.foursquare.com/2012/08/09/foursquare-now-uses-elastic-search-and-on-a-related-note-slashem-also-works-with-elastic-search/">Foursquare</a>, Yfrog (<a href="http://elasticsearch-users.115913.n3.nabble.com/some-ES-stats-at-yfrog-com-td3759891.html">950 millions de documents indexés)</a>, <a href="http://geeks.aretotally.in/klout-search-elasticsearch-play-framework-scala-akka">Klout</a> ainsi que la <a href="http://devoxx.pilato.fr/keynote/assets/fallback/index.html">Douane Française</a>.</p> <p>Largement adopté par les utilisateurs et les développeurs, le projet compte de <a href="https://github.com/elasticsearch/elasticsearch/graphs/contributors">nombreux contributeurs</a> et approche les <a href="https://github.com/elasticsearch/elasticsearch/stargazers">3000 followers</a> sur son <a href="https://github.com/elasticsearch/elasticsearch">repository GitHub</a>.</p> <h5>Au cœur du projet, Apache Lucene</h5> <p>ElasticSearch est basé sur l'excellente librairie <a href="http://lucene.apache.org/">Apache Lucene</a>. Cette librairie existe depuis de nombreuses années et est au cœur de nombreux moteurs de recherche open source (le plus connu étant <a href="http://lucene.apache.org/solr/">Apache SolR</a>). Elle fournit toutes les classes Java nécessaires à l'indexation de documents et à l'exécution des requêtes de recherches.</p> <p>Lucene étant une librairie de bas niveau, son intégration dans des applications gérant un grand nombre de requêtes concurrentes et/ou un gros volume de données peut s'avérer très complexe. Il n'est d'ailleurs pas rare de rencontrer quelques problèmes de performances, notamment lorsque la taille des index dépassent plusieurs giga octets ou lorsque les requêtes de recherche doivent être parallélisées.</p> <p>ElasticSearch facilite l'utilisation de Lucene en intégrant la librairie dans une application Java <a href="http://www.elasticsearch.org/guide/reference/modules/">modulaire</a>, facilement configurable et capable de fonctionner en cluster. Un raccourci rapide serait de définir ElasticSearch comme un <em>Lucene dans le Cloud</em>, c'est à dire un cluster de Lucene.</p> <p>Ce fonctionnement en cluster, très simple à configurer, permet de répartir automatiquement le volume des données indexées et la charge d'utilisation sur l'ensemble des machines appartenant au cluster. Pour absorber plus de données ou plus de charge, il suffira d'ajouter de nouvelles machines au cluster.</p> <p>A noter que la prochaine version d'ElasticSearch (<a href="http://www.elasticsearch.org/blog/2012/10/23/0.19.11-0.20.0.RC1-released.html">0.21</a>) intégrera la version 4 de Lucene et ses nombreuses nouveautés.</p> <h5>Les fonctionnalités clés d'ElasticSearch</h5> <p>ElasticSearch fournit toutes les fonctionnalités d'un moteur de recherche nouvelle génération&nbsp;: les recherches par mots clés «&nbsp;à la Google », les recherches par combinaison de critères et de filtres, le tri et la pagination des résultats, la gestion des synonymes, l'extraction de texte à partir de documents binaires, l'analyse et la <a href="http://en.wikipedia.org/wiki/Faceted_search">navigation par facettes</a>... Seules manquent à l'appel les fonctionnalités de correction orthographique automatique (<em>spellchecking</em>) et de <a href="https://github.com/elasticsearch/elasticsearch/issues/256">groupage de résultats</a>, très utiles, qui ne sont pas encore présentes dans ElasticSearch mais qui seront implémentées dans les prochaines versions du projet.</p> <p>ElasticSearch dispose en plus de son propre système de plugins. Ce système permet d'ajouter facilement de nouvelles fonctionnalités au moteur de recherche. Parmi les <a href="http://www.elasticsearch.org/guide/reference/modules/plugins.html">plugins existants</a>, on peut trouver des analyseurs syntaxiques, des interfaces d'administrations ou encore des connecteurs pour indexer différentes sources de données (bases de données relationnelles ou NoSQL, annuaire LDAP, système de fichiers, flux RSS etc).</p> <p>ElasticSearch apporte aussi son lot de fonctionnalités innovantes telles que:</p> <ul> <li>la <strong>percolation</strong>, qui permet de pré enregistrer des recherches puis d’injecter un document et d’obtenir en retour la liste des recherches correspondants à ce document</li> <li>les <strong>rivières</strong> (“Rivers”), qui sont des connecteurs permettant d’indexer un flot continu de documents issus de sources de données variées</li> </ul> <p>Ces deux fonctionnalités feront l'objet d'un futur article.</p> <p>Enfin, ElasticSearch dispose d'une API <a href="http://www.elasticsearch.org/guide/reference/api/"><acronym title="REpresentational State Transfert">REST</acronym></a> très complète permettant d'utiliser le moteur de recherche avec de simples requêtes HTTP/JSON.</p> <h4>Installer ElasticSearch</h4> <p>L'unique pré requis à l'installation d'ElasticSearch est la présence de <a href="http://java.sun.com/javase/downloads/index.jsp">Java 6</a> sur la machine destinée à héberger le moteur de recherche.</p> <p>Pour vérifier si Java 6 est installé, il suffit d'exécuter la commande suivante dans un shell *nix ou une invite de commande Windows:</p> <pre> $ java -version java version &quot;1.6.0_25&quot; Java(TM) SE Runtime Environment (build 1.6.0_25-b06) </pre> <p>ElasticSearch est distribué sous forme d'archive zip/tar.gz ou de package .deb à l'adresse <a href="http://www.elasticsearch.org/download/">http://www.elasticsearch.org/download/</a>.</p> <p>Une fois l'archive téléchargée, il suffit de la décompresser pour installer ElasticSearch:</p> <pre> $ unzip elasticsearch-0.19.11.zip Archive: elasticsearch-0.19.11.zip creating: elasticsearch-0.19.11/ creating: elasticsearch-0.19.11/bin/ creating: elasticsearch-0.19.11/config/ inflating: elasticsearch-0.19.11/bin/elasticsearch.bat … </pre> <p>A ce stade, le répertoire d'installation comprend les répertoires suivants:</p> <ul> <li><strong>bin</strong>, qui contient le script de lancement  <code>elasticsearch</code> et le script d'installation de plugins <code>plugin</code> (respectivement <code>elasticsearch.bat</code> et <code>plugin.bat</code> pour Windows)</li> <li><strong>config</strong>, qui contient les fichiers de configuration d'ElasticSearch (<code>elasticsearch.yml</code>) et celui des logs (<code>logging.yml</code>). Nous reviendrons sur ces deux fichiers dan
s un instant.</li> <li><strong>lib</strong>, qui contient certaines librairies utilisées par ElasticSearch</li> </ul> <p>L'installation d'ElasticSearch est désormais terminée, nous allons pouvoir démarrer ElasticSearch.</p> <p>Pour cela, il faut exécuter le script <code>elasticsearch</code> (ou <code>elasticsearch.bat</code> sous Windows):</p> <pre> $ cd elasticsearch-0.19.11/ $ ./bin/elasticsearch </pre> <p>A noter qu'ElasticSearch peut aussi être installé en tant que service du système d'exploitation en suivant <a href="http://www.elasticsearch.org/guide/reference/setup/installation.html">la procédure suivante</a>.</p> <p>Au lancement, ElasticSearch va créer de nouveaux répertoires&nbsp;:</p> <ul> <li><strong>data</strong>, destiné à contenir les données indexées</li> <li><strong>logs</strong>, qui contient les fichiers de journalisation</li> <li><strong>work</strong>, qui contient des fichiers temporaires nécessaires au fonctionnement du moteur de recherche</li> </ul> <p>Ce second répertoire va nous être utile pour vérifier le bon lancement d'ElasticSearch. Il suffit de regarder le contenu du principal fichier de log <code>elasticsearch.log</code> dont voici un exemple:</p> <pre> $ tail -f logs/elasticsearch.log [2012-10-30 15:36:48,100][INFO ][node ] [Elsie-Dee] {0.19.11}[11660]: initializing ... [2012-10-30 15:36:48,104][INFO ][plugins ] [Elsie-Dee] loaded [], sites [] [2012-10-30 15:36:49,708][INFO ][node ] [Elsie-Dee] {0.19.11}[11660]: initialized [2012-10-30 15:36:49,708][INFO ][node ] [Elsie-Dee] {0.19.11}[11660]: starting ... [2012-10-30 15:36:49,784][INFO ][transport ] [Elsie-Dee] bound_address {inet[/0:0:0:0:0:0:0:0:9300]}, publish_address {inet[/192.168.1.17:9300]} [2012-10-30 15:36:52,835][INFO ][cluster.service ] [Elsie-Dee] new_master [Elsie-Dee][pt99cavSTGuyiDies_6oqw][inet[/192.168.1.17:9300]], reason: zen-disco-join (elected_as_master) [2012-10-30 15:36:52,875][INFO ][discovery ] [Elsie-Dee] elasticsearch/pt99cavSTGuyiDies_6oqw [2012-10-30 15:36:52,904][INFO ][http ] [Elsie-Dee] bound_address {inet[/0:0:0:0:0:0:0:0:9200]}, publish_address {inet[/192.168.1.17:9200]} [2012-10-30 15:36:52,904][INFO ][node ] [Elsie-Dee] {0.19.11}[11660]: started </pre> <p>La dernière ligne du fichier confirme qu'ElasticSearch est bien démarré (<code>started</code>) et indique son numéro de version <code>(0.19.11</code>) ainsi que le PID du processus (<code>11660</code>).</p> <p>Une seconde façon de vérifier qu'ElasticSearch s'est correctement lancé consiste à ouvrir l'URL <a href="http://localhost:9200/">http://localhost:9200/</a> dans un navigateur Web. En effet, ElasticSearch écoute par défaut sur le port 9200 pour répondre aux éventuelles requêtes HTTP REST qui lui sont faites.</p> <p>La réponse du serveur confirme le bon fonctionnement d'ElasticSearch et rappelle le numéro de la version installée&nbsp;:</p> <pre> {     &quot;ok&quot;: true,     &quot;status&quot;: 200,     &quot;name&quot;: &quot;Elsie-Dee&quot;,     &quot;version&quot;: {         &quot;number&quot;: &quot;0.19.11&quot;,         &quot;snapshot_build&quot;: false     },     &quot;tagline&quot;: &quot;You Know, for Search&quot; } </pre> <p>Et voilà, nous venons d'installer et de démarrer un premier nœud ElasticSearch&nbsp;!</p> <h4>Introduction au cluster ElasticSearch</h4> <h5>Qu'est-ce qu'un cluster ElasticSearch&nbsp;?</h5> <p>Un <strong>cluster</strong> ElasticSearch est composé de plusieurs nœuds qui communiquent entre eux. Chaque <strong>nœud</strong> (node en anglais) correspond à une instance d'ElasticSearch en cours d'exécution, et peut être ajouté ou retiré du cluster même lorsque ce dernier est en train de fonctionner.</p> <p>En général, il n'existe qu'un nœud par machine (physique ou virtuelle) mais il est tout à fait possible d'installer plusieurs nœuds sur une même machine, notamment pour des besoins de tests ou de montée de version. Dans ce cas et afin d'éviter les collisions, chaque nœud ElasticSearch écoutera automatiquement sur des ports différents&nbsp;: 9200 pour le premier, 9201 pour le second, 9202 pour le troisième etc</p> <p>Au sein d'un cluster, chaque nœud est identifié par un nom unique. S'il n'est pas configuré, le nœud s'attribue automatiquement un nom de super héro parmi <a href="https://github.com/elasticsearch/elasticsearch/blob/master/src/main/resources/config/names.txt">une liste plutôt bien fournie</a>.</p> <p>Ainsi, dans notre exemple le nœud a automatiquement pris le nom «&nbsp;Elsie-Dee »: <code><a href="2012-10-30 15:36:52,904" title="2012-10-30 15:36:52,904">2012-10-30 15:36:52,904</a><a href="INFO" title="INFO">INFO</a><a href="node" title="node">node</a> <a href="Elsie-Dee" title="Elsie-Dee">Elsie-Dee</a> {0.19.11}<a href="11660" title="11660">11660</a>: started</code></p> <p>Afin d'éviter que le nom d'un nœud ne change à chaque démarrage, il suffit de configurer la propriété <code>node.name</code> dans le fichier de configuration <em>config/elasticsearch.yml</em>:</p> <pre> node.name: &quot;noeud-0&quot; </pre> <p>Au prochain démarrage, notre nœud s'appellera «&nbsp;noeud-0 ».</p> <p>Un cluster ElasticSearch est identifié de façon unique par un nom. L'appartenance à un cluster est configurée au niveau de chacun des nœuds, dans la propriété <code>cluster.name</code> du fichier de configuration. Par défaut, cette propriété n'est pas renseignée et le nom de cluster est tout simplement «&nbsp;elasticsearch ».</p> <p>Une bonne pratique consiste à configurer un nom de cluster qui reflète la plateforme (test, integration, production etc) et la version d'ElasticSearch (ce qui peut être pratique pour les futures montées de version)&nbsp;:</p> <pre> cluster.name: &quot;test-0.19.11&quot; </pre> <p>Lorsqu'un nœud démarre, il va chercher à rejoindre le cluster de nom cluster.name. Pour cela, il interroge le réseau à la recherche d'autres nœuds en cours d'exécution et vérifie si ces nœuds appartiennent au même cluster que lui. Si d'autres nœuds sont identifiés, le nœud va rejoindre le cluster. Lorsqu'aucun cluster n'est trouvé, le nœud va en créer un pour lui-même en se déclarant comme nœud maître, dont le rôle est de coordonner certaines opérations au sein du cluster.</p> <p>L'étape d'identification des clusters et des nœuds est appelée <a href="http://www.elasticsearch.org/guide/reference/modules/discovery/index.html">''Discovery''</a> (découverte en français) et peut s'effectuer de différentes manières.</p> <h5>Découverte en Multicast</h5> <p>La configuration par défaut utilise le <a href="http://fr.wikipedia.org/wiki/Multicast">''Multicast''</a> pour l'identification des autres nœuds. Dans ce mode, le nœud en cours de démarrage envoie une requête multicast (en utilisant le port 54328 et l'adresse de groupe 224.2.2.4) sur le réseau, et seuls les nœuds ayant le même nom de cluster lui répondront. Le nœud pourra ensuite rejoindre le cluster.</p> <p>Ce mode de découverte est très pratique car il permet de démarrer rapidement un cluster ElasticSearch, mais dans certains cas il est préférable de le désactiver pour éviter que n'importe quel nœud rejoigne le cluster par erreur.</p> <p>La désactivation du Multicast s'effectue dans le fichier de configuration de chaque nœud&nbsp;:</p> <pre> discovery.zen.ping.multicast.enabled: false </pre> <h5>Découverte en Unicast</h5> <p>La configuration <em>Unicast</em> est utile lorsque le Multicast n'est pas disponible ou lorsqu'il est désactivé. Ce mode nécessite de configurer chaque nœud du cluster avec les adresses et numéros (ou plage) de ports des autres nœuds du cluster, de la façon suivante&nbsp;:</p> <pre> discovery.zen.ping.unicast.hosts: [&quot;host1&quot;, &quot;host2:9300&quot;, &quot;host3[9300-9399]&quot;] </pre> <p>A noter qu'il n'est pas nécessaire qu'un nœud connaisse la totalité des autres nœuds du cluster. Il lui suffit de pouvoir se connecter à 1 seul autre nœud du cluster pour pouvoir rejoindre ce dernier. La configuration Unicast est conseillée pour les clusters utilisés en production.</p> <h5>Découverte EC2</h5> <p>Ce dernie
r mode de découverte permet à des nœuds ElasticSearch de fonctionner correctement sur la plateforme d'Amazon Elastic Compute Cloud (Amazon EC2).</p> <h5>Communication entre nœuds</h5> <p>Les nœuds d'un cluster ElasticSearch utilisent le framework JBoss Netty pour communiquer entre eux. La communication s'effectue par défaut sur le port 9300 et les messages sont composés d'objets Java sérialisés. Pour cette raison, il est fortement conseillé que tous les nœuds d'un même cluster fonctionnent avec une même version du projet.</p> <p>Au sein d'un cluster, les nœuds se pingent à intervalles réguliers (par défaut toutes les 3 secondes). Si un nœud met trop de temps à répondre ou qu'il ne répond tout simplement pas, il sera exclu du cluster par les autres nœuds. Ce cas peut arriver sur un réseau peu performant ou lorsqu'une coupure réseau survient.</p> <p>Par défaut, chaque nœud du cluster est capable de réaliser toutes les opérations de recherches et d'indexation de documents, de maintenance du cluster ou de réponse aux requêtes HTTP REST effectuées par les clients. Si besoin, les options de configuration <code>http.enabled</code> (active/désacte le support HTTP REST) et <code>node.data</code> (active/désactive le stockage de données indexées) permettent de spécifier le comportement d'un nœud. Plusieurs protocoles peuvent être utilisés pour <a href="/index.php?post/2012/05/21/Elasticsearch-Modes-de-communication-et-plugin-MQ">communiquer avec les noeuds d'un cluster ElasticSearch</a>: Apache Thrift, ZeroMQ, memcached... Ces protocoles peuvent être utilisés à la place ou en plus de l'API REST HTTP déjà disponible.</p> <p>Maintenant que le fonctionnement d'un cluster ElasticSearch est un peu plus clair, nous pouvons ajouter un nouveau nœud à notre cluster.</p> <h5>Ajout d'un nœud au cluster (avec une configuration Multicast)</h5> <p>Le nœud «&nbsp;noeud-0&nbsp;» précédemment installé est en cours d'exécution. Nous pouvons dupliquer son répertoire de configuration puis supprimer ses répertoires <em>data</em>, <em>work</em> et <em>logs</em>:</p> <pre> $ cp -R elasticsearch-0.19.11/ elasticsearch-0.19.11_bis/ $ rm -rf elasticsearch-0.19.11_bis/data/ $ rm -rf elasticsearch-0.19.11_bis/work/ $ rm -rf elasticsearch-0.19.11_bis/logs/ </pre> <p>Ensuite, il suffit d'éditer le fichier de configuration et indiquer un autre second nom de nœud <em>noeud-1</em>:</p> <pre> $ vi elasticsearch-0.19.11_bis/config/elasticsearch.yml node.name : noeud-1 </pre> <p>Puis démarrer ce second  nœud:</p> <pre> $ cd elasticsearch-0.19.11_bis/ $ ./bin/elasticsearch -f </pre> <p>Le fichier de log du <em>noeud-1</em> confirme bien que le nœud maitre <em>noeud-0</em> a été identifié&nbsp;:</p> <pre> [INFO ][cluster.service] [noeud-1] detected_master [noeud-0][VPfFZ-1lSY20-AgYrr-jCg][inet[/192.168.1.17:9300]], added {[noeud-0][VPfFZ-1lSY20-AgYrr-jCg][inet[/192.168.1.17:9300]],}, reason: zen-disco-receive(from master [[noeud-0][VPfFZ-1lSY20-AgYrr-jCg][inet[/192.168.1.17:9300]]]) </pre> <p>Et le fichier de log du <em>noeud-0</em> confirme l'ajout du nouveau nœud <em>noeud-1</em> au cluster&nbsp;:</p> <pre> [INFO ][cluster.service] [noeud-0] added {[noeud-1][gURACdMrRSWQfS-Y97Z-pQ][inet[/192.168.1.17:9301]],}, reason: zen-disco-receive(join from node[[noeud-1][gURACdMrRSWQfS-Y97Z-pQ][inet[/192.168.1.17:9301]]]) </pre> <p>Ce premier cluster ElasticSearch composé de 2 nœuds nous sera utile pour mieux appréhender l'indexation de documents avec l'API REST.</p> <h5>Installer le plugin ElasticSearch-Head</h5> <p>Le plugin <a href="http://mobz.github.com/elasticsearch-head/">ElasticSearch-Head</a> fournit une interface d’administration pour ElasticSearch. Très simple à utiliser, ce plugin permet de consulter la liste des documents indexés dans ElasticSearch, d’effectuer des recherches ou encore d’avoir une représentation graphique de l’état d’un cluster.</p> <p>L’installation d’un plugin doit être réalisée lorsque le noeud est arrêté. L’API REST dispose d’une commande pour arrêter tous les noeuds d’un cluster:</p> <pre> $ curl -XPOST 'http://localhost:9200/_shutdown' </pre> <p>Une fois le noeud arrêté, nous pouvons installer le plugin avec la commande suivante:</p> <pre> $ cd elasticsearch-0.19.11/ $ ./bin/plugin -install mobz/elasticsearch-head -&gt; Installing mobz/elasticsearch-head... Trying https://github.com/downloads/mobz/elasticsearch-head/elasticsearch-head-0.19.11.zip... Trying https://github.com/mobz/elasticsearch-head/zipball/v0.19.11... Trying https://github.com/mobz/elasticsearch-head/zipball/master... Downloading .................................DONE Identified as a _site plugin, moving to _site structure ... Installed head </pre> <p>Le plugin est désormais installé. Nous pouvons alors redémarrer le premier noeud <em>noeud-0</em>:</p> <pre> $ cd elasticsearch-0.19.11/ $ ./bin/elasticsearch -f </pre> <p>Puis le second noeud <em>noeud-1</em>:</p> <pre> $ cd elasticsearch-0.19.11_bis/ $ ./bin/elasticsearch -f </pre> <p>Et enfin ouvrir l’URL <a href="http://localhost:9200/_plugin/head/">http://localhost:9200/_plugin/head/</a> dans un navigateur. L’interface du plugin présente bien les 2 noeuds du cluster (le noeud master est surligné en orange):<br /></p> <p><img src="/wp-content/uploads/2015/07/.elasticsearch_premiers_pas_partie_1_image_1_m.jpg" alt="ElasticSearch-Head 1" style="display:block; margin:0 auto;" title="ElasticSearch-Head 1" /> <br /></p> <h4>Indexer et rechercher avec ElasticSearch</h4> <h5>Vue générale de l'API REST</h5> <p>ElasticSearch propose une API REST permettant de réaliser tous types d'opérations. Cette API utilise le format JSON pour les requêtes et les réponses et supporte les principales méthodes HTTP (GET, DELETE, PUT et POST).</p> <p>Elle est utilisée de la façon suivante&nbsp;:</p> <pre> http://host:port/[index]/[type]/[_action|id] </pre> <p>Avec</p> <ul> <li>index 	: Nom de l'index sur lequel porte l'opération</li> <li>type 	: Nom du type de document</li> <li>_action	: Nom de l'action à effectuer</li> <li>id 	: Identifiant du document</li> </ul> <h5>Indexation de documents</h5> <p>Voici un exemple d'indexation d'un document de type <code>formation</code> dans un index nommé <code>zenika</code> avec l'API REST:</p> <pre> $ curl -XPUT 'http://localhost:9200/zenika/formation/1' -d '{     &quot;titre&quot;: &quot;Formation ElasticSearch&quot;,     &quot;sous-titre&quot;: &quot;Savoir utiliser et configurer ElasticSearch, le moteur de recherche seconde génération&quot;,     &quot;formateurs&quot;: [         {             &quot;prenom&quot;: &quot;Tanguy&quot;,             &quot;nom&quot;: &quot;Leroux&quot;         }     ],     &quot;jours&quot;: 3,     &quot;url&quot;: &quot;http://www.zenika.com/formation_elasticsearch.html&quot;,     &quot;sessions&quot;: [         {             &quot;ville&quot;: &quot;Paris&quot;,             &quot;dates&quot;: [                 &quot;20/02/2013&quot;,                 &quot;29/05/2013&quot;             ]         },         {             &quot;ville&quot;: &quot;Lyon&quot;,             &quot;dates&quot;: [                 &quot;18/03/2013&quot;,                 &quot;17/06/2013&quot;             ]         }     ] }' </pre> <p>La réponse renvoyée par ElasticSearch nous indique que l'indexation s'est bien passé (<code>ok</code> est à <code>true</code>) et nous rappelle l'index, le type et l'identifiant du document indexé&nbsp;:</p> <pre> {     &quot;ok&quot;: &quot;true&quot;,     &quot;_index&quot;: &quot;zenika&quot;,     &quot;_type&quot;: &quot;formation&quot;,     &quot;_id&quot;: &quot;1&quot;,     &quot;_version&quot;: 1 } </pre> <p>Le triplet index / type / id correspond aux coordonnées du document, qui permettent de le retrouver parmi tous les documents indexés dans le moteur de recherche.</p> <p>Un rafraîchissement de la page du plugin ElasticSearch-Head va nous montrer comment l’index <code>zenika</code> a été automatiquement répartit sur nos 2 noeuds:<br /></p> <p><img src="/wp-content/uploads/2015/07/.elasticsearch_premiers_pas_partie_1_image_2_m.jpg" alt="ElasticSearc
h-Head 2" style="display:block; margin:0 auto;" title="ElasticSearch-Head 2" /> <br /></p> <p>L’onglet <em>Browser</em> (“Navigateur”) du plugin est très pratique pour consulter la liste des documents indexés:<br /></p> <p><img src="/wp-content/uploads/2015/07/.elasticsearch_premiers_pas_partie_1_image_3_m.jpg" alt="ElasticSearch-Head" style="display:block; margin:0 auto;" title="ElasticSearch-Head" /> <br /></p> <h5>Rechercher des documents</h5> <p>Lorsque les coordonnées d'un document sont connues, il est très facile de le retrouver avec l'API REST. Il suffit d'effectuer une requête GET&nbsp;:</p> <pre> $ curl -XGET 'http://localhost:9200/zenika/formation/1' </pre> <p>Et ElasticSearch nous renvoie le document&nbsp;:</p> <pre> {     &quot;_id&quot;: &quot;1&quot;,     &quot;_index&quot;: &quot;zenika&quot;,     &quot;_source&quot;: {         …     },     &quot;_type&quot;: &quot;formation&quot;,     &quot;_version&quot;: 1,     &quot;exists&quot;: true } </pre> <p>Mais la plupart du temps, les coordonnées du document ne sont pas connues. Il nous faut donc retrouver le document avec l'aide de  quelques mots-clés.</p> <p>L'API REST dispose d'un raccourci très pratique pour cela, dans lequel les mots-clés sont passés dans le paramètre <em>q</em> (pour «&nbsp;query ») de l'URL:</p> <pre> $ curl -XGET 'http://localhost:9200/zenika/formation/_search?q=elasticsearch' </pre> <p>ElasticSearch nous renverra alors une liste de documents correspondants aux résultats de la recherche:</p> <pre> {     &quot;_shards&quot;: {         &quot;failed&quot;: 0,         &quot;successful&quot;: 5,         &quot;total&quot;: 5     },     &quot;hits&quot;: {         &quot;hits&quot;: [             {                 &quot;_id&quot;: &quot;1&quot;,                 &quot;_index&quot;: &quot;zenika&quot;,                 &quot;_score&quot;: 0.057534903,                 &quot;_source&quot;: {                     ...                 },                 &quot;_type&quot;: &quot;formation&quot;             }         ],         &quot;max_score&quot;: 0.057534903,         &quot;total&quot;: 1     },     &quot;timed_out&quot;: false,     &quot;took&quot;: 11 } </pre> <p>Il est aussi possible de rechercher un mot clé dans un champ précis du document indexé:</p> <pre> $ curl -XGET 'http://localhost:9200/zenika/formation/_search?q=jours:3' </pre> <p>Ou encore retrouver un document à partir d'un fragment de mot clé:</p> <pre> $ curl -XGET 'http://localhost:9200/zenika/formation/_search?q=tang*' </pre> <p>Mais le moteur de recherche est capable de retrouver un document même si le mot clé n'est pas identique à celui indexé et si aucun index ni type ne sont connus:</p> <pre> $ curl -XGET 'http://localhost:9200/_search?q=genration~' </pre> <p>Mais comment ElasticSearch arrive-t-il à retrouver un tel document?</p> <p>Et bien grâce aux <strong>index</strong>, aux <strong>types de documents</strong> et aux <strong>mappings</strong>, qui feront l'objet de notre prochain article&nbsp;!</p>