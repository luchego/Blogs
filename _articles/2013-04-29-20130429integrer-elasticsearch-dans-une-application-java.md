---
ID: 414
post_title: >
  Intégrer ElasticSearch dans une
  application Java
author: Gérald Quintana
post_date: 2013-04-29 10:00:00
post_excerpt: |
  <p>Ca y est, c'est décidé, nous allons utiliser ElasticSearch comme moteur de recherche dans notre application Java. J'ai suivi la <a href="http://www.zenika.com/formation_elasticsearch.html" hreflang="fr">formation Zenika</a>, j'ai installé mon premier cluster ElasticSearch et fait quelques gammes avec cURL. Bref tout est prêt, il n'y a plus qu'à l'intégrer dans notre application Java.</p> <p>Cet article est issu d'un cas réel (aucun noeud ElasticSearch n'a été blessé durant le tournage), il présente les problématiques rencontrées et les solutions apportées.</p>
layout: post
permalink: http://blog.zenika-offres.com/?p=414
published: true
slide_template:
  - ""
---
<p>Ca y est, c'est décidé, nous allons utiliser ElasticSearch comme moteur de recherche dans notre application Java. J'ai suivi la <a href="http://www.zenika.com/formation_elasticsearch.html" hreflang="fr">formation Zenika</a>, j'ai installé mon premier cluster ElasticSearch et fait quelques gammes avec cURL. Bref tout est prêt, il n'y a plus qu'à l'intégrer dans notre application Java.</p> <p>Cet article est issu d'un cas réel (aucun noeud ElasticSearch n'a été blessé durant le tournage), il présente les problématiques rencontrées et les solutions apportées.</p>
<!--more-->
<h3>Accéder à ElasticSearch depuis le monde Java</h3> <p>Manipuler le moteur de recherche depuis la ligne de commande avec cURL comme décrit dans la documentation, c'était du gâteau. Pour faire la même chose depuis mon application Web, plusieurs solutions sont possibles.</p> <p>La solution la plus rapide, et peut-être la plus performante, est d'y accéder directement depuis le navigateur web, car invoquer l'API REST avec peu de JavaScript et d'Ajax est très simple. La seule difficulté serait de sécuriser le serveur ElasticSearch pour éviter qu'un vandale ne mette à mal notre cluster neuf et étincelant.</p> <p>Pour rassurer le responsable sécurité informatique qui est réticent à l'idée d'exposer ce nouveau produit à l'extérieur, ou bien parce que nous utilisons un framework Web server-side, nous allons plutôt attaquer ElasticSearch depuis notre serveur Java. Moyennant quelques librairies Java pour écrire un client JSON/REST, on pourrait attaquer l'API REST comme cURL ou n'importe quel navigateur. Mais ElasticSearch propose déjà une API Java pratique et productive, il y a juste deux bémols:</p> <ol> <li>Nous devrons introduire une dépendance entre notre application et ElasticSearch et embarquer le Jar adéquat</li> <li>La communication ne s'effectuera plus en HTTP mais dans le protocole natif d'ElasticSearch.</li> </ol> <p>Cette API Java d'ElasticSearch est du type fluent, elle est à la fois pratique et concise. Le fait d'avoir un typage fort plutôt que du JSON est très rassurant pour écrire ses premières requêtes sans se tromper. Cette API propose deux façons de se connecter aux serveurs ElasticSearch:</p> <ul> <li>Node Client: l'application devient elle même un noeud du cluster: elle gère elle-même une partie des données et délègue aux autres noeuds le reste du temps. Elle est capable de déterminer quel noeud possède telle donnée, ce qui permet d'optimiser l'accès aux données. Il est toutefois possible de faire en sorte que l'application soit un noeud sans données en désactivant l'option <code>node.data</code>.</li> <li>Transport Client: l'application est un client neutre du cluster, elle ne sait pas comment sont réparties les données.</li> </ul> <p>Les deux offrent au final la même interface Client pour accéder aux données:</p> <pre> // Node Client  Node node = NodeBuilder.nodeBuilder().node();  Client client = node.client();  // Transport Client  Client client = new TransportClient()    .addTransportAddress(new InetSocketTransportAddress("host1", 9300))    .addTransportAddress(new InetSocketTransportAddress("host2", 9300));</pre> <h3>Injection de dépendances</h3> <p>Accéder à un moteur de recherche, c'est au final comme accéder à une base de données: on écrit des données (indexer un document) et on lit des données (rechercher des documents). Le patron de conception DAO peut s'appliquer ici aussi, sauf qu'au lieu d'injecter une DataSource JDBC, ce sera un Client ElasticSearch.</p> <pre> @Repository  public class ContactDaoImpl implements ContactDao {    @Autowired    private Client client;</pre> <p>Pour que Spring injecte ce Client, il faut qu'il soit capable de l'instancier et de le configurer. L'API fluent d'ElasticSearch est très pratique pour établir cette connexion, mais elle n'est pas compatible avec l'approche POJO/JavaBean de Spring. Pour intégrer les deux, un peu de plomberie est donc nécessaire, sinon il existe des librairies comme <a href="https://github.com/dadoonet/spring-elasticsearch" hreflang="en">spring-elasticsearch</a> ou <a href="https://github.com/BioMedCentralLtd/spring-data-elasticsearch" hreflang="en">spring-data-elasticsearch</a>.</p> <p>Par injection de dépendances, on pourra aisément injecter un Node Client ou un Transport Client à nos DAO ElasticSearch sans que cela n'impacte notre code. Et on ne va pas s'en priver, on n'utilisera pas la même Client en fonction du besoin.</p> <ul> <li>Au run-time (la vraie vie de l'application): on utilisera le Transport Client pour se connecter au véritable cluster ElasticSearch.</li> <li>Au test-time (les tests automatisés): on utilisera le Node Client pour obtenir une version embarquée d'ElasticSearch que l'on pourra démarrer/arrêter à souhait. C'est un peu comme si on utilisait H2DB ou HSQLDB pour tester un DAO JDBC.</li> </ul> <p><img src="/wp-content/uploads/2015/07/elasticsearch-java-1.png" alt="Organisation du code" style="display:block; margin:0 auto;" title="Organisation du code" /></p> <h3>Accès aux données</h3> <p>Faire une recherche dans ElasticSearch avec l'API fluent se résume à:</p> <pre>   public List&lt;Contact&gt; searchContacts(String query) throws IOException {        // Prépare la recherche        SearchRequestBuilder searchRequest=client.prepareSearch("contact")                .setTypes("simple")                .setQuery(queryString(query));        // Execute la requête        SearchResponse searchResponse=searchRequest.execute().actionGet();        // Extrait les résultats        SearchHit<a href=""></a> searchHits = searchResponse.getHits().getHits();        List&lt;Contact&gt; contacts=new ArrayList&lt;Contact&gt;(searchHits.length);        for(SearchHit searchHit:searchHits) {            Contact contact=objectMapper.readValue(searchHit.source(), Contact.class);            contacts.add(contact);        }        return contacts;    }</pre> <p>L'extraction des résultats, c'est à dire la conversion des SearchHits en objets Java n'est pas obligatoire. On s'appuie ici sur <a href="https://github.com/FasterXML/jackson-databind" hreflang="en">Jackson</a> pour automatiser la conversion du JSON en POJO, on aurait aussi bien pu manipuler directement l'arbre JSON:</p> <pre>           String nom=(String) searchHit.sourceAsMap().get("nom");</pre> <p>Si on utilise le système de <a href="http://www.elasticsearch.org/guide/reference/api/search/fields/" hreflang="en">fields</a>, c'est à dire qu'on sélectionne dans la requête les champs que l'on souhaite avoir dans la réponse (un peu comme une projection en SQL), la syntaxe différente est moins propice à l'utilisation d'un mapper Objet/JSON:</p> <pre>           String nom=searchHit.field("nom").value();</pre> <p>L'écriture est tout aussi simple que la lecture: Pour indexer quelque chose il faut d'abord en faire du JSON:</p> <pre>       public void indexContact(Contact contact) throws JsonProcessingException {            // Sérialise en JSON            String source=objectMapper.writeValueAsBytes(contact)            // Prépare l'indexation            IndexRequestBuilder indexRequest=client.prepareIndex("contact", "simple")                    .setId(Integer.toString(contact.getId()))                    .setSource(source);            // Exécute l'indexation            indexRequest                    .setRefresh(true)                    .execute().actionGet();        }</pre> <p>Par défaut la mise à jour des index ne s'effectue pas immédiatement, il est donc probable que le document ne soit pas parmi les résultats de recherche dans les secondes qui suivent l'indexation. En ajoutant le <code>setRefresh(true)</code>, on attend que cette indexation soit réellement faite continuer: le temps de réponse s'en retrouve dégradé. Bref, il faut choisir en performance et cohérence.</p> <h3>Tests automatisés</h3> <p>Evidemment, tester est une nécessité, mais le fait qu'on utilise des concepts complexes comme les analyseurs, le scoring ou les recherches floues rend la chose encore plus vraie. Un petit changement sur un mapping peut avoir de grands effets sur les recherches.</p> <p>Heureusement ce n'est pas compliqué à tester, on procède comme pour une base de données:</p> <ol> <li>Démarrer un ElasticSearch embarqué,</li> <li>Supprimer les index,</li> <li>Créer un index et les mappings de types,</li> <li>Peupler l'index, c'est à dire indexer des documents,</li> <li>Tester le DAO,</li> <li>Retour à l'étape 2 ou 5,</li> <li>Arrêter ElasticSearch.</li> </ol> <p>Pour gérer le cycle de vie d'ElasticSearch (étapes 1 et 7), on va se servir de Spring et de spring-elasticsearch.</p> <pre> &lt;beans ... &gt;    &lt;es:node id="n
ode" settingsFile="es-test.properties" /&gt;    &lt;es:client id="client" node="node" /&gt;    &lt;context:component-scan base-package="com.zenika.elasticsearch.dao"/&gt;  &lt;/beans&gt;</pre> <pre> @RunWith(SpringJUnit4ClassRunner.class)  @ContextConfiguration  public class ContactDaoImplTest {      @Autowired      private ContactDao contactDao;      @Autowired      private Client client;</pre> <p>Dans la configuration ElasticSearch, il vaut mieux activer l'option <code>node.local</code> pour éviter que des clusters ne se forment entre machines. On peut aussi désactiver le stockage des index sur disque avec <code>index.store.type=memory</code>:</p> <pre> cluster.name=test-cluster  node.name=test-node  node.local=true  index.store.type=memory  path.logs=target/es/log  path.data=target/es/data</pre> <p>En ce qui concerne les étapes 2 à 4, pour préparer les index et les données, on utilise <a href="https://github.com/tlrx/elasticsearch-test" hreflang="en">elasticsearch-test</a> qui est à ElasticSearch, ce que DBUnit est à JDBC:</p> <pre>   private EsSetup esSetup;    @Before    public void setUp() {        esSetup=new EsSetup(client);        esSetup.execute(            deleteAll(), // Etape 2            createIndex("contact") // 3                .withMapping("simple", fromClassPath("contact-simple.mapping.json"))                .withData(fromClassPath("contact-simple.bulk.json")) // Etape 4        );    }</pre> <p>Puis le test en lui même s'écrit naturellement:</p> <pre>   @Test    public void testSearchContacts() throws IOException {        List&lt;Contact&gt; contacts=contactDao.searchContacts("jean");        assertEquals(1,contacts.size());        assertEquals("Jean-Claude",contacts.get(0).getPrenom());    }</pre> <p>Pour remplir les index à l'étape 4, elasticsearch-test utilise l'API <a href="http://www.elasticsearch.org/guide/reference/api/bulk/" hreflang="en">Bulk</a> qui est destinée à l'indexation de gros volumes de données. Le format de données Bulk est adapté au chargement en masse mais pas vraiment aux tests unitaires. Il est destiné à être consommé par une machine, les fichiers deviennent rapidement illisibles pour un humain pour deux raisons:</p> <ul> <li>Imposer l'alternance une ligne d'ordre/une ligne de données proscrit l'indentation du JSON, les commentaires...</li> <li>Un fichier bulk pris dans son ensemble ne forme pas un document JSON syntaxiquement correct: La vérification syntaxique des IDEs est rapidement déboussolée.</li> </ul> <pre> {"index": {"_index": "contact", "_type": "simple", "_id": "1" } }  {"id":1, "nom": "Dusse", "prenom": "Jean-Claude", "email": "jc@bronzes.fr", "societe": "Bronzés"}  {"index": {"_index": "contact", "_type": "simple", "_id": "2" } }  {"id":2, "nom": "Morin", "prenom": "Bernard", "email": "bernard@bronzes.fr", "societe": "Bronzés"}</pre> <p>Dans le contexte des tests, je pense qu'il faut se servir du format bulk uniquement comme d'un format intermédiaire, et le générer à partir d'objets Java ou d'un autre document JSON (<a href="https://gist.github.com/gquintana/20d761cf087c8e4d940b" hreflang="fr">façon XSLT/JSONT</a>).</p> <p>Pour tester l'indexation de documents, on procèdera de la mème manière que pour les recherches, mais il faudra se méfier du rafraîchissement des index (<code>setRefresh(true)</code>).</p> <pre> @Test  public void testIndexContact() throws IOException {    assertEquals(3, esSetup.count("contact").intValue());    contactDao.indexContact(new Contact(4,"Gisèle", "André","gigi@bronzes.fr","Bronzés"));    assertEquals(4, esSetup.count("contact").intValue());  }</pre> <h3>Conclusion</h3> <p>Introduire ElasticSearch dans une application Java existante n'est pas sorcier. On retrouve les patterns et les outils (Spring, JUnit) que l'on a l'habitude de manipuler avec les bases de données relationnelles, et il n'y a guère plus de plomberie à mettre en place. Les choix qui ont été faits sont discutables et ne sont pas peut-être pas généralisables: c'est une approche possible parmi d'autres.</p>