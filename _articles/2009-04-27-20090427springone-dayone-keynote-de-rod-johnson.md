---
ID: 239
post_title: 'SpringOne, DayOne &#8211; Keynote de Rod Johnson'
author: ocroisier
post_date: 2009-04-27 23:56:00
post_excerpt: |
  <p><a href="http://europe.springone.com/europe-2009">SpringOne</a>, premier jour.</p> <p>Malgré un ciel bas et lourd (avec un soupçon de pluie) pesant comme un couvercle sur l'esprit gémissant en proie aux longs ennuis des passagers du Thalys (2h de retard, merci bien), l'équipe Zenika a rejoint Amsterdam à temps pour assister à la fameuse Keynote de <a href="http://europe.springone.com/europe-2009/speaker/Rod+Johnson">Rod Johnson</a>, qui ouvrait la conférence.</p> <p>Au programme, son analyse de l'actualité et sa vision du futur de Java, puis un tour d'horizon du portfolio SpringSource, et même quelques annonces exclusives. Suivez le guide&nbsp;!</p>
layout: post
permalink: http://blog.zenika-offres.com/?p=239
published: true
---
<p><a href="http://europe.springone.com/europe-2009">SpringOne</a>, premier jour.</p> <p>Malgré un ciel bas et lourd (avec un soupçon de pluie) pesant comme un couvercle sur l'esprit gémissant en proie aux longs ennuis des passagers du Thalys (2h de retard, merci bien), l'équipe Zenika a rejoint Amsterdam à temps pour assister à la fameuse Keynote de <a href="http://europe.springone.com/europe-2009/speaker/Rod+Johnson">Rod Johnson</a>, qui ouvrait la conférence.</p> <p>Au programme, son analyse de l'actualité et sa vision du futur de Java, puis un tour d'horizon du portfolio SpringSource, et même quelques annonces exclusives. Suivez le guide&nbsp;!</p>
<!--more-->
<h3>The future of Java innovation</h3> <p>Personne n'est resté insensible à l'annonce du <a href="http://www.sun.com/third-party/global/oracle/index.jsp">rachat de Sun Microsystems par Oracle</a>, pour la modique somme de 7.4 milliards de dollars (soit 237,95€ au cours du jour), et la communauté Java se demande à quelle sauce elle va être mangée. Quelles sont les motivations d'Oracle&nbsp;? Est-ce un simple mouvement stratégique pour couper l'herbe numérique sous le pied d'IBM, ou cela fait-il partie d'une stratégie à long terme&nbsp;? La "marque" Java représente-t-elle un réel atout commercial&nbsp;?</p> <p>En réalité, Oracle pourrait bel et bien s'octroyer des avantages concurrentiels importants en influançant les JCP (et donc la norme JavaEE), mais ne pourra pas contrôler l'écosystème Java dans son ensemble, grâce à son immense communauté, dont Google, la fondation Apache, et... IBM en personne, ne serait-ce qu'à travers la fondation Eclipse. Et le langage lui-même est maintenant open-source&nbsp;! Alors pourquoi racheter Sun&nbsp;? Pour sa rentabilité&nbsp;? Depuis plusieurs années, le soleil s'est voilé et la firme est déficitaire sur la plupart de ses activités, bien loin des 70% de marge auxquels Oracle est habitué&nbsp;; de sévères restructurations et de nombreux licenciements sont donc à craindre. Pour insuffler une vision nouvelle dans le monde de la programmation opensource, alors&nbsp;? Oracle n'est clairement pas connu pour ses prouesses en la matière, ni pour la puissance de son innovation.</p> <p>La question est donc&nbsp;: quelles répercussions tout ceci aura-t-il sur SpringSource ?<br />
En réalité, Rod Johnson se frotte les mains. Cela peut paraître surprenant, mais voici pourquoi. La plateforme JavaEE actuelle est beaucoup trop lourde et trop complexe - la fuite des développeurs au profit de plateformes plus légères comme PHP ou Grails ou Ruby on Rails en est un symptôme flagrant, et son passage sous la houlette d'Oracle ne risque pas d'améliorer les choses.</p> <p>Plus préoccupant, les reproches envers la norme Java EE rejaillissent sur la plateforme entière, qui est pourtant très performante (VM, serveurs d'application, etc.). Il est donc temps de réagir avant que sa mauvaise réputation ne l'enterre définitivement. Pour cela, il faut disposer d'outils et de technologies plus productifs pour regagner l'estime des développeurs et rester compétitif par rapport aux autres plateformes - voire même, <a href="http://www.caucho.com/resin-3.0/quercus/">les attaquer sur leur propre terrain.</a></p> <p>Et c'est très exactement sur ce créneau que se positionne SpringSource, avec une offre intégrée couvrant l'ensemble du cycle de vie des applications&nbsp;: développement, déploiement et exploitation.<br />
Rod Johnson en a d'ailleurs profité pour annoncer la prochaine gratuité de STS (<a href="http://www.springsource.com/products/sts">SpringSource Tools Suite</a>), et le support de Groovy et Grails au sein de l'IDE&nbsp;!</p> <h3>Développement</h3> <p>Pour améliorer la productivité du développeur, SpringSource met en avant deux produits&nbsp;:</p> <ul> <li>Groovy et <a href="http://www.grails.org/">Grails</a>, rachetés récemment, qui attirent de nombreux développeurs (Java et non-Java).</li> <li>ROO, un générateur de code intelligent, qui cible les développeurs Java maîtrisant déjà Spring. A ce propos, un concours sera organisé prochainement pour renommer le projet&nbsp;; commencez à réfléchir&nbsp;!</li> </ul> <h5>ROO in action</h5> <p>Ben Alex, développeur de ROO et déjà à l'origine de <a href="http://static.springsource.org/spring-security/site/index.html">SpringSecurity</a> (anciennement Acegi Security), a réalisé une démonstration des principales fonctionnalités de l'outil.</p> <p>Tout d'abord, il a créé un répertoire vide destiné à accueillir le nouveau projet, et à partir duquel il a lancé ROO depuis une ligne de commandes. ROO se présente lui-même sous la forme d'un shell et offre des fonctionnalités d'autocomplétion contextuelle intelligente. A partir de ce shell, voici comment il a créé un projet de type SpringMVC/Spring/Hibernate&nbsp;:</p> <pre class="bash code bash" style="font-family:inherit"><ol><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;"><span style="color: #666666; font-style: italic;"># Créer une hiérarchie standard de répertoires, et définit le &quot;package racine&quot; du projet</span></div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;">create project <span style="color: #660033;">-topLevelPackage</span> com.springsource.vote</div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;"><span style="color: #666666; font-style: italic;"># Ajouter Hibernate et une base de données HSQLDB</span></div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;"><span style="color: #c20cb9; font-weight: bold;">install</span> jpa <span style="color: #660033;">-provider</span> HIBERNATE <span style="color: #660033;">-database</span> HYPERSONIC</div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;"><span style="color: #666666; font-style: italic;"># Créer un objet persistant &quot;Choice&quot;, dans le sous-package &quot;domain&quot;</span></div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;">new persistent class jpa <span style="color: #660033;">-name</span> ~.domain.Choice</div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;"><span style="color: #666666; font-style: italic;"># Ajouter un champ persistant &quot;namingChoice&quot; de type String, avec de règles de validation (Cf. JSR 303)</span></div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;">add field string <span style="color: #660033;">-fieldName</span> namingChoice <span style="color: #660033;">-notNull</span> <span style="color: #660033;">-sizeMin</span> <span style="color: #000000;">3</span><span style="color: #ff0000;">&quot;</span></div></li></ol></pre> <p>Il a ensuite suffi d'importer le projet dans Eclipse, après avoir utilité Maven pour générer ses métadonnées (<code>mvn eclipse:eclipse</code>).</p> <h5>ROO sous le capot</h5> <p>Jetons donc un oeil au code généré.<br />
Nous voyons bien la classe <code>Choice</code>, qui possède comme prévu un champ <code>namingChoice</code>. Mais à part ça, la classe semble bien vide (pas d'accesseurs), et la couche DAO habituelle est introuvable. Où est donc passé tout le code&nbsp;?</p> <p>En réalité, ROO utilise <a href="http://www.eclipse.org/aspectj/">AspectJ</a> pour incorporer dynamiquement des méthodes aux classes métier. Ce processus est piloté par des annotations. Par exemple,</p> <ul> <li><code>@RooEntity</code> fournit des méthodes de persistance&nbsp;: merge(), update(), findAll(), findById(Long id), etc.</li> <li><code>@RooJavaBean</code> injecte les accesseurs (<em>getters/setters</em>)</li> <li><code>@RooToString</code> fournit une implémentation basique de la méthode toString().</li> </ul> <p>Notez que toutes les annotations possèdent le même préfixe.</p> <p>Ce qui est intéressant, c'est que cet assemblage est parfaitement reconnu par SpringSource Tool Suite (c'est-à-dire Spring IDE), et qu'il est possible d'appeler ces méthodes sur la classe de manière transparente&nbsp;: l'autocomplétion fonctionne, le débuggage pas à pas aussi...</p> <p>La classe métier devient donc le point central de l'application, dont l'architecture devient "domain-oriented" plutôt que "layer-oriented". Les habitués de Grails ne seront pas dépaysés. Par contre, l'aspect "auto-magique" de la chose et la disparition du découpage en couches risquent de déplaire à plus d'un architecte.</p> <h5>Roo et le web</h5> <p>ROO permet également de générer des contrôleurs SpringMVC. <br />
Pour cela, retour au shell ROO&nbsp;:</p> <pre class="bash code bash" style="font-family:inherit"><ol><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;"><span style="color: #666666; font-style: italic;"># Création d'un contrôleur avec mapping automatique de type [REST|http://blog.springsource.com/2009/03/08/rest-in-spring-3-mvc/]</span></div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;">new controller automatic <span style="color: #660033;">-name</span> ~.web.ChoiceController</div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;"><span style="color: #666666; font-style: italic;"># Après la création d'une entité Vote associant une IP à un Choice particulier, génération du contrôleur associé, cette fois en mode manuel : ce sera au développeur de l'implémenter et de gérer ses mappings.</span></div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;">new controller manual <span style="color: #660033;">-name</span> ~.web.VoteController</div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;"><span style="color: #666666; font-style: italic;"># Installation et configuration de Spring Security. Cela crée un fichier de configuration &quot;application-context-security.xml&quot; qui protège les contrôleurs. Si on essaie d'accéder à une ressource protégée, la page de login apparaît, et après login, l'utilisateur est redirigé vers la page initialement demandée.</span></div></li><li style="font-weight: normal;"><div style="font-family: monospace; font-weight: normal; font-style: normal; margin:0; padding:0; background:inherit;"><span style="color: #c20cb9; font-weight: bold;">install</span> security</div></li></ol></pre> <p>Il ne reste plus qu'à déployer l'application web sur un serveur (au hasard, Tomcat ?). <br /></p> <p>Il est important de savoir que l'application n'est jamais liée à ROO, ni pendant le développement, ni au runtime (mais AslectJ est requis). De plus, il est capable de faire du <em>reverse-engineering</em> sur les classes modifiées par le développeur directement dans l'IDE (hors shell ROO), et de s'adapter aux modifications.<br /></p> <p>A propos d'IDE, ROO est déjà intégré à Spring IDE et se présente sous la forme d'une vue proposant un shell.</p> <h5>ROO vs Grails</h5> <p>Il n'y a pas de réelle concurrence entre Grails et ROO. Le premier cible les développeurs non-java, ou ceux qui veulent utiliser un langage dynamique (groovy)&nbsp;; le second vise plutôt les développeurs souhaitant conserver tous les avantages du typage dynamique et la performance d'un langage compilé.</p> <h3>Déploiement</h3> <p>Une fois les applications développées, il faut bien les déployer sur des serveurs.<br /></p> <p>Rod Johnson prédit la mort prochaine des serveurs d'applications traditionnels&nbsp;: trop de fonctionnalités inutiles, approche monolithique ("one size fits all"), peu productifs... Pour lui, l'avenir est aux serveurs légers, qui répondent à 80% des besoins d'entreprise, et plus important encore, aux attentes des développeurs.</p> <p>SpringSource, qui embauche les principaux développeurs de Tomcat, propose deux produits qui en dérivent :<a href="http://www.springsource.com/products/tcserver">TC server</a> et <a href="http://www.springsource.com/products/dmserver">DM server</a>.</p> <ul> <li>TC Server est un Tomcat auquel ont été ajoutées des fonctionnalités de management avancées (grâce à AMS, l'Advanced Management Suite), et qui bénéficie d'un support commercial. Rod Jonhson vient d'officialiser la sortie de la version 1.0.</li> <li>DM Server intègre une couche <a href="http://www.osgi.org/">OSGi</a> qui permet de découper les applications en modules autonomes, afin de gérer leurs cycles de vies de manière indépendante.</li> </ul> <p>Pour conclure cette session, <a href="http://europe.springone.com/europe-2009/speaker/Jennifer+Hickey">Jennifer Hickey</a> a réalisé une rapide démonstration des capacités de la console d'administration AMS&nbsp;:</p> <ul> <li>Déploiement de l'application sur un cluster de TC Servers, d'un seul clic</li> <li>Modification des paramètres des serveurs (taille de la mémoire et des pools de connexion)</li> <li>Monitoring des serveurs avec remontée d'alertes</li> </ul> <h3>Conclusion</h3> <p>Cette première séance était fort intéressante, puisqu'elle nous a permis de connaître la vision et la stratégie de SpringSource sur le marché des applications Java, surtout à la lumière des événements récents, et de découvrir leurs nouveaux produits.</p>