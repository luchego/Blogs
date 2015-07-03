---
ID: 110
post_title: 'Introduction au framework Camel &#8211; Partie 2'
author: ggiamarchi
post_date: 2012-03-22 09:17:00
post_excerpt: |
  <p>Dans un <a href="/index.php?post/2010/03/14/Introduction-au-framework-Camel-Partie-1">précédent article</a> nous avions introduit le framework de routage <a href="http://camel.apache.org">Camel</a>. A présent, entrons un peu plus dans le vif du sujet avec l'étude de quelques types de <em>endpoints</em>. Dans ce second article nous allons voir plus en détail l'utilisation des <em>endpoints</em> en nous focalisant plus spécifiquement sur certains d'entre eux.</p>
layout: post
permalink: http://blog.zenika-offres.com/?p=110
published: true
---
<p>Dans un <a href="/index.php?post/2010/03/14/Introduction-au-framework-Camel-Partie-1">précédent article</a> nous avions introduit le framework de routage <a href="http://camel.apache.org">Camel</a>. A présent, entrons un peu plus dans le vif du sujet avec l'étude de quelques types de <em>endpoints</em>. Dans ce second article nous allons voir plus en détail l'utilisation des <em>endpoints</em> en nous focalisant plus spécifiquement sur certains d'entre eux.</p>
<!--more-->
<p>Comme vu précédemment, Camel a vocation à être utilisé pour résoudre des problématiques d'intégration de systèmes. Il peut pour cela être utilisé au sein d'un <acronym title="Enterprise Service Bus">ESB</acronym> ou d'un serveur d'applications, mais il peut également être utilisé en mode <em>standalone</em> dans un programme Java comme vu dans l'article précédent.</p> <p>Par ailleurs, dans les exemples toutes les routes sont définies en utilisant le <acronym title="Domain-specific language">DSL</acronym> Java car il s'agit de la méthode la plus flexible et la moins verbeuse mais je rappelle qu'il est toujours possible de décrire une route en XML.</p> <h3>Les Endpoints</h3> <p>Nous avions vu ce que sont les <em>endpoints</em> dans l'article précédent. Nous allons maintenant voir plus précisément comment les utiliser.</p> <p><strong>Endpoints et Composants</strong></p> <p>La notion de composant dans Camel est un concept clé qu'il est important de bien appréhender. Le terme peut être confusant car ce que l'on nomme composant est en réalité une fabrique de <em>endpoints</em>. Un composant implémente l'interface <code><a href="http://camel.apache.org/maven/current/camel-core/apidocs/org/apache/camel/Component.html">Component</a></code>. Elle définie principalement la méthode <code>createEndpoint(String uri)</code> qui renvoie un nouvel objet de type <code><a href="http://camel.apache.org/maven/current/camel-core/apidocs/org/apache/camel/Endpoint.html">Endpoint</a></code>.</p> <p><strong>Utilisation des URI</strong></p> <p>On peut tout à fait faire référence à des objets de type <code>Endpoint</code> dans la définition des routes mais souvent on préférera utiliser des URI. Dans l'article précédent nous avions déjà mis cela en oeuvre en définissant la route&nbsp;:</p> <pre class="java code java" style="font-family:inherit">from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/in&quot;</span><span style="color: #000000;">&#41;</span>.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/out&quot;</span><span style="color: #000000;">&#41;</span>;</pre> <p>Cette route qui relie deux <em>endpoints</em> de type <code>file</code> va déplacer tous les fichiers arrivant dans le répertoire <code>in</code> vers le répertoire <code>out</code>.</p> <p>Lors de l'appel à la méthode <code>from</code>, Camel va instancier un <em>endpoint</em> <code>file</code> si c'est la première fois qu'il apparaît dans la définition des routes, sinon la référence vers le <em>endpoint</em> existant sera utilisée. Plus simplement, deux URI identiques impliquent un unique <em>endpoint</em>.</p> <p>L'utilisation des options d'URI permet de configurer le <em>endpoint</em>. Par exemple, pour le consommateur de fichiers ci-dessus je peux définir le délais après lequel il va consommer pour la première fois et l'intervalle de temps entre deux consommations.</p> <pre class="java code java" style="font-family:inherit">from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/in?consumer.initialDelay=2000&amp;consumer.delay=5000&quot;</span><span style="color: #000000;">&#41;</span>.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/out&quot;</span><span style="color: #000000;">&#41;</span>;</pre> <p>La page de documentation du <a href="http://camel.apache.org/file2.html">composant file</a> donne l'ensemble des options possibles pour ce type de <em>endpoint</em>.</p> <p>Camel fourni environ 70 <a href="http://camel.apache.org/components.html">types de endpoint</a>. De plus, en cas de besoins spécifiques il est toujours possible de créer ses propres <em>endpoints</em>.</p> <h3>Les endpoints "direct" et "seda"</h3> <p>Contrairement au <em>endpoint</em> <code>file</code>, <code>direct</code> et <code>seda</code> ne représentent pas une ressource physique. Il s'agit de <em>endpoints</em> logiques. ils sont utilisés pour communiquer à l'intérieur du contexte Camel.</p> <p><strong>direct :</strong></p> <p>Gardons l'exemple de transfert de fichiers du répertoire <code>in</code> vers le répertoire <code>out</code> et ré-écrivons cette route en deux morceaux de route reliés entre elles par un <em>endpoint</em> de type <code>direct</code>.</p> <pre class="java code java" style="font-family:inherit">from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/in&quot;</span><span style="color: #000000;">&#41;</span>.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;direct:filemove&quot;</span><span style="color: #000000;">&#41;</span>; &nbsp; from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;direct:filemove&quot;</span><span style="color: #000000;">&#41;</span>.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/out&quot;</span><span style="color: #000000;">&#41;</span>;</pre> <p>Un <em>endpoint</em> <code>direct</code> se comporte de manière synchrone et fonctionne donc en mode <em>In/Out</em>. Le producteur et le consommateur s'exécutent dans le même thread. Ce endpoint n'a pas d'option et son utilisation n'est jamais plus compliquée que dans l'exemple ci-dessus.</p> <p><strong>seda :</strong></p> <p>A l'inverse du endpoint <code>direct</code>, <code>seda</code> se comporte de manière asynchrone et fonctionne donc en mode <em>In Only</em>. Le producteur et le consommateur s'exécutent dans deux threads distincts. Cela permet de paralléliser la consommation sur le <em>endpoint</em>. L'option <code>concurrentConsumers</code> permet de spécifier le nombre de consommateurs en concurrence sur le <em>endpoint</em>. L'implémentation de <code>seda</code> se base sur une <code><a href="http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/BlockingQueue.html">BlockingQueue</a></code> pour la gestion de l'asynchronisme, tout comme le endpoint <code>vm</code> qui est d’ailleurs une extension du endpoint <code>seda</code>. La différence entre les deux réside dans le fait que <code>vm</code> permet de communiquer entre plusieurs contextes Camel.</p> <pre class="java code java" style="font-family:inherit">from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/in&quot;</span><span style="color: #000000;">&#41;</span>.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;seda:process&quot;</span><span style="color: #000000;">&#41;</span>; &nbsp; from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;seda:process?concurrentConsumers=5&quot;</span><span style="color: #000000;">&#41;</span>.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/out&quot;</span><span style="color: #000000;">&#41;</span>;</pre> <p>Dans cet exemple, plusieurs consommateurs travaillent en parallèle. Pour en avoir le cœur net, il est possible d'ajouter des traces de log sur les routes à l'aide du composant <code>log</code>.</p> <pre class="java code java" style="font-family:inherit">from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/in&quot;</span><span style="color: #000000;">&#41;</span> 		.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;log:Producteur&quot;</span><span style="color: #000000;">&#41;</span> 		.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;seda:process&quot;</span><span style="color: #000000;">&#41;</span>; &nbsp; from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;seda:process?concurrentConsumers=5&quot;</span><span style="
color: #000000;">&#41;</span> 		.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;log:Consommateur&quot;</span><span style="color: #000000;">&#41;</span> 		.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/out&quot;</span><span style="color: #000000;">&#41;</span>;</pre> <p>Exécutons le programme et plaçons 5 fichiers dans le répertoire <code>in</code> simultanément, puis observons les traces produites lors de l'exécution. On constate en regardant les identifiants des threads que la consommation est parallèlisée comme nous le souhaitions.</p> <pre> 25637 [Camel (camelContext) thread #0 - file://C:/factures/in] INFO Producteur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile1.txt]]] 25641 [Camel (camelContext) thread #0 - file://C:/factures/in] INFO Producteur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile2.txt]]] 25641 [Camel (camelContext) thread #4 - seda://process] INFO Consommateur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile1.txt]]] 25642 [Camel (camelContext) thread #0 - file://C:/factures/in] INFO Producteur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile3.txt]]] 25642 [Camel (camelContext) thread #0 - file://C:/factures/in] INFO Producteur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile4.txt]]] 25643 [Camel (camelContext) thread #0 - file://C:/factures/in] INFO Producteur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile5.txt]]] 25645 [Camel (camelContext) thread #5 - seda://process] INFO Consommateur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile2.txt]]] 25645 [Camel (camelContext) thread #1 - seda://process] INFO Consommateur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile3.txt]]] 25646 [Camel (camelContext) thread #3 - seda://process] INFO Consommateur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile4.txt]]] 25646 [Camel (camelContext) thread #2 - seda://process] INFO Consommateur - Exchange[ExchangePattern:InOnly, BodyType:org.apache.camel.component.file.GenericFile, Body:[Body is file based: GenericFile[c:facturesinfile5.txt]]] </pre> <p>Il existe une autre possibilité pour paralléliser des traitements sans utiliser de <em>endpoint</em> de type <code>seda</code>. Il s'agit de la méthode <code>threads()</code>.</p> <pre class="java code java" style="font-family:inherit">from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/in&quot;</span><span style="color: #000000;">&#41;</span> 		.<span style="color: #000000;">threads</span><span style="color: #000000;">&#40;</span><span style="color: #cc66cc;">5</span><span style="color: #000000;">&#41;</span>                     <span style="color: #808080; font-style: italic;">// Maximum 5 threads</span> 		.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/out&quot;</span><span style="color: #000000;">&#41;</span>;</pre> <p>La différence fondamentale entre cette approche et l'usage d'un <em>endpoint</em> de type <code>seda</code> réside dans le fait qu'ici Camel va utiliser un pool de threads dont la taille est variable en fonction de l'activité sur cette route, alors qu'avec <code>seda</code> le nombre de consommateurs - et donc de threads - est fixé au départ et reste inchangé au cours de l’exécution.</p> <h3>Effectuer des traitements</h3> <p>Nous avons vu les bases du routage avec Camel nous permettant de faire transiter des messages de <em>endpoint</em> en <em>endpoint</em>. Mais le routage n'est qu'une des composantes permettant de résoudre la plupart des problématiques d'intégration. Nous aurons également besoin d'effectuer des traitements.</p> <p>Pour cela, nous allons voir deux manières d'implémenter des traitements&nbsp;:</p> <ul> <li>les processeurs</li> <li>le composant <code>bean</code></li> </ul> <p><strong>Les processeurs</strong></p> <p>Un processeur est une classe qui implémente l'interface <code><a href="http://camel.apache.org/maven/current/camel-core/apidocs/org/apache/camel/Processor.html">Processor</a></code>. Cette interface définie une unique méthode <code>process</code> qu'il faut donc implémenter.</p> <p>Par exemple&nbsp;:</p> <pre class="java code java" style="font-family:inherit"><span style="color: #7F0055; font-weight: bold;">package</span> com.<span style="color: #000000;">zenika</span>.<span style="color: #000000;">camel</span>.<span style="color: #000000;">samples</span>.<span style="color: #000000;">beans</span>; &nbsp; <span style="color: #7F0055; font-weight: bold;">public</span> <span style="color: #7F0055; font-weight: bold;">class</span> FileProcessor <span style="color: #7F0055; font-weight: bold;">implements</span> org.<span style="color: #000000;">apache</span>.<span style="color: #000000;">camel</span>.<span style="color: #000000;">Processor</span> <span style="color: #000000;">&#123;</span> 	<span style="color: #7F0055; font-weight: bold;">void</span> process<span style="color: #000000;">&#40;</span>Exchange exchange<span style="color: #000000;">&#41;</span> <span style="color: #7F0055; font-weight: bold;">throws</span> <span style="color: #000000;">Exception</span> <span style="color: #000000;">&#123;</span> 		<span style="color: #000000;">System</span>.<span style="color: #000000;">out</span>.<span style="color: #000000;">println</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;FileProcessor.process()&quot;</span><span style="color: #000000;">&#41;</span>; 	<span style="color: #000000;">&#125;</span> <span style="color: #000000;">&#125;</span></pre> <p>le paramètre <code>exchange</code> de la méthode nous permet entre autres d'accéder au message et éventuellement de le modifier. Ici nous faisons un simple <code>println</code> donc le message en sortie du processeur restera inchangé.</p> <p>Déclaration du bean dans le contexte Spring</p> <pre class="xml code xml" style="font-family:inherit"><span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;bean</span> <span style="color: #000066;">id</span>=<span style="color: #ff0000;">&quot;fileProcessor&quot;</span> <span style="color: #000066;">class</span>=<span style="color: #ff0000;">&quot;com.zenika.camel.samples.beans.FileProcessor&quot;</span> <span style="color: #000000; font-weight: bold;">/&gt;</span></span></pre> <p>Nous pouvons ensuite faire transiter nos messages par ce bean.</p> <pre class="java code java" style="font-family:inherit">from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/in&quot;</span><span style="color: #000000;">&#41;</span> 		.<span style="color: #000000;">processRef</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;fileProcessor&quot;</span><span style="color: #000000;">&#41;</span> 		.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/out&quot;</span><span style="color: #000000;">&#41;</span>;</pre> <p><strong>Le composant "bean"</strong></p> <p>Le type de <em>endpoint</em> <code>bean</code> va nous permettre de router des messages vers une classe
 java qui implémente le traitement à effectuer.</p> <p>Contrairement au processeur vu précédemment, cette classe n'a nullement besoin d'implémenter une interface précise ni de respecter un certain format pour les signatures des méthodes.</p> <p>Commençons par créer une classe qui déclare une méthode.</p> <pre class="java code java" style="font-family:inherit"><span style="color: #7F0055; font-weight: bold;">package</span> com.<span style="color: #000000;">zenika</span>.<span style="color: #000000;">camel</span>.<span style="color: #000000;">samples</span>.<span style="color: #000000;">beans</span>; &nbsp; <span style="color: #7F0055; font-weight: bold;">public</span> <span style="color: #7F0055; font-weight: bold;">class</span> FileBean <span style="color: #000000;">&#123;</span> 	<span style="color: #7F0055; font-weight: bold;">public</span> <span style="color: #7F0055; font-weight: bold;">void</span> processFile<span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span> 		<span style="color: #000000;">System</span>.<span style="color: #000000;">out</span>.<span style="color: #000000;">println</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;FileBean.processFile()&quot;</span><span style="color: #000000;">&#41;</span>; 	<span style="color: #000000;">&#125;</span> <span style="color: #000000;">&#125;</span></pre> <p>Déclaration du bean dans le contexte Spring</p> <pre class="xml code xml" style="font-family:inherit"><span style="color: #009900;"><span style="color: #000000; font-weight: bold;">&lt;bean</span> <span style="color: #000066;">id</span>=<span style="color: #ff0000;">&quot;fileBean&quot;</span> <span style="color: #000066;">class</span>=<span style="color: #ff0000;">&quot;com.zenika.camel.samples.beans.FileBean&quot;</span> <span style="color: #000000; font-weight: bold;">/&gt;</span></span></pre> <p>Nous pouvons ensuite faire transiter nos messages par ce bean</p> <pre class="java code java" style="font-family:inherit">from<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/in&quot;</span><span style="color: #000000;">&#41;</span> 	.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;bean:fileBean?method=processFile&quot;</span><span style="color: #000000;">&#41;</span> 	.<span style="color: #000000;">to</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;file://C:/factures/out&quot;</span><span style="color: #000000;">&#41;</span>;</pre> <p>On peut aussi invoquer le bean - de manière équivalente - comme suit</p> <pre class="java code java" style="font-family:inherit">.<span style="color: #000000;">beanRef</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;fileBean&quot;</span>, <span style="color: #888888;">&quot;processFile&quot;</span><span style="color: #000000;">&#41;</span></pre> <p>Ou encore invoquer un bean en passant sa référence plutot qu'un id Spring.</p> <pre class="java code java" style="font-family:inherit">.<span style="color: #000000;">bean</span><span style="color: #000000;">&#40;</span><span style="color: #7F0055; font-weight: bold;">new</span> FileBean<span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>, <span style="color: #888888;">&quot;processFile&quot;</span><span style="color: #000000;">&#41;</span></pre> <p>Dans cet exemple, nous fournissons à Camel le nom de la méthode que nous souhaitons invoquer. En réalité, ce paramètre est optionnel. Dans le cas ou le nom de la méthode n'est pas renseigné, Camel se base sur plusieurs euristiques pour tenter de déterminer quelle est la méthode à invoquer. Je n'entre pas dans le détail du mécanisme mis en place par Camel pour effectuer le choix de la méthode, il est détaillé dans la <a href="http://camel.apache.org/bean-binding.html">documentation</a>.</p> <p>Laisser Camel déterminer la méthode à invoquer n'est pas forcément recommandé. En cas d'évolutions futures du bean par exemple - ajout ou modification d'une signature - il peut être difficile de prévoir les impacts sur le choix qui sera fait par Camel au cours de l'exécution. Personnellement, je ne recommande pas d'utiliser cette possibilité si elle n'apporte pas de valeur ajoutée.</p> <h3>A suivre...</h3> <p>Dans un prochain article, nous étudierons quelques uns des <acronym title="Enterprise Integration Patterns">EIP</acronym> (Enterprise Integration Patterns) les plus courants. Les <acronym title="Enterprise Integration Patterns">EIP</acronym> fournissent des solutions à des problématiques récurrentes dans de nombreux chantiers d'intégration de systèmes.</p> <p>Camel implémente un grand nombre d'<acronym title="Enterprise Integration Patterns">EIP</acronym>. Nous verrons que leur utilisation apporte une réelle valeur ajoutée lorsqu'il s'agit d'implémenter des logiques de routage complexes.</p>