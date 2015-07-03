---
ID: 123
post_title: >
  HATEOAS paging with Spring MVC and
  Spring Data JPA
author: Arnaud Cogoluègnes
post_date: 2012-06-15 09:45:00
post_excerpt: |
  <p>In a <a href="/index.php?post/2012/06/04/HATEOAS-with-Spring-MVC-REST">previous post</a>, I exposed the principles of <a href="http://en.wikipedia.org/wiki/HATEOAS">HATEOAS</a> and illustrated these principles with an implementation based on <a href="http://www.springsource.org/spring-framework">Spring MVC</a> and <a href="https://github.com/SpringSource/spring-hateoas">Spring HATEOAS</a>. In this post, I'll go further with the implementation of a paged REST web services that follows the guidelines of HATEOAS. This implementation is based again on Spring MVC and Spring HATEOAS, and uses <a href="http://www.springsource.org/spring-data/jpa">Spring Data JPA</a>'s built-in paging features for the database backend.</p>
layout: post
permalink: http://blog.zenika-offres.com/?p=123
published: true
---
<p>In a <a href="/index.php?post/2012/06/04/HATEOAS-with-Spring-MVC-REST">previous post</a>, I exposed the principles of <a href="http://en.wikipedia.org/wiki/HATEOAS">HATEOAS</a> and illustrated these principles with an implementation based on <a href="http://www.springsource.org/spring-framework">Spring MVC</a> and <a href="https://github.com/SpringSource/spring-hateoas">Spring HATEOAS</a>. In this post, I'll go further with the implementation of a paged REST web services that follows the guidelines of HATEOAS. This implementation is based again on Spring MVC and Spring HATEOAS, and uses <a href="http://www.springsource.org/spring-data/jpa">Spring Data JPA</a>'s built-in paging features for the database backend.</p>
<!--more-->
<h2>Paged REST controller, no HATEOAS</h2> <p>The first version of our controller doesn't implement HATEOAS guidelines, as the representation of a page contains the content itself (contact records) and information about the page itself (number, size, etc), but no link to navigate to the previous or to the next page.</p> <p>Here is the first version of our controller:</p> <pre class="java code java" style="font-family:inherit">@Controller @RequestMapping<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;/contacts&quot;</span><span style="color: #000000;">&#41;</span> <span style="color: #7F0055; font-weight: bold;">public</span> <span style="color: #7F0055; font-weight: bold;">class</span> ContactController <span style="color: #000000;">&#123;</span> &nbsp;   @Autowired ContactRepository contactRepository;   @RequestMapping<span style="color: #000000;">&#40;</span>value=<span style="color: #888888;">&quot;/pages&quot;</span>,method=RequestMethod.<span style="color: #000000;">GET</span><span style="color: #000000;">&#41;</span>   @ResponseBody   <span style="color: #7F0055; font-weight: bold;">public</span> Page<span style="color: #000000;">&lt;</span>Contact<span style="color: #000000;">&gt;</span> contactsPages<span style="color: #000000;">&#40;</span>@RequestParam <span style="color: #7F0055; font-weight: bold;">int</span> page,@RequestParam <span style="color: #7F0055; font-weight: bold;">int</span> size<span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span>     <span style="color: #000000;">Pageable</span> pageable = <span style="color: #7F0055; font-weight: bold;">new</span> PageRequest<span style="color: #000000;">&#40;</span>       page,size,<span style="color: #7F0055; font-weight: bold;">new</span> Sort<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;id&quot;</span><span style="color: #000000;">&#41;</span>     <span style="color: #000000;">&#41;</span>;     Page<span style="color: #000000;">&lt;</span>Contact<span style="color: #000000;">&gt;</span> pageResult = contactRepository.<span style="color: #000000;">findAll</span><span style="color: #000000;">&#40;</span>pageable<span style="color: #000000;">&#41;</span>;     <span style="color: #7F0055; font-weight: bold;">return</span> pageResult;   <span style="color: #000000;">&#125;</span> <span style="color: #000000;">&#125;</span></pre> <p>Some explanations:</p> <ul> <li>the client provides the number of the requested page and the size with HTTP parameters. The end of the URL would look like <code>/contacts/pages?page=1&amp;size=5</code>.</li> <li>the controller uses Spring Data JPA paging support. It builds a <code>PageRequest</code> object from the HTTP parameters, chooses to sort by ID, calls the Spring Data JPA repository, and gets a <code>Page</code>.</li> <li>The controller returns the <code>Page</code>, Spring MVC takes care of generating some JSON from the <code>Page</code>.</li> </ul> <p>Here is what the representation looks like in this version (content first and then page information):</p> <pre> {    &quot;content&quot;:[       {          &quot;id&quot;:1,          &quot;firstname&quot;:&quot;Joe&quot;,          &quot;lastname&quot;:&quot;Dalton&quot;,          &quot;age&quot;:37       },       {          &quot;id&quot;:2,          &quot;firstname&quot;:&quot;William&quot;,          &quot;lastname&quot;:&quot;Dalton&quot;,          &quot;age&quot;:35       },       ... more records ...    ],    &quot;size&quot;:5,    &quot;number&quot;:0,    &quot;sort&quot;:[       {          &quot;direction&quot;:&quot;ASC&quot;,          &quot;property&quot;:&quot;id&quot;,          &quot;ascending&quot;:true       }    ],    &quot;totalPages&quot;:3,    &quot;numberOfElements&quot;:5,    &quot;totalElements&quot;:12,    &quot;firstPage&quot;:true,    &quot;lastPage&quot;:false } </pre> <p>In the previous post, we worked on making the records HATEOAS: a contact record was a summary of the contact resource and contained a link to GET to the detail of the contact. We could apply the exact same techniques in this post, but we'll leave the records alone and focus on the page itself. We want a page to expose links to the previous/next/first/last pages.</p> <p>We will make these links available by wrapping a page in a link-aware custom class.</p> <h2>The link-aware page wrapper</h2> <p>The page wrapper has the following goals:</p> <ul> <li>exposing the page - that's why it implements <code>Page</code> and delegates all the methods to the underlying page (the one returned by the repository)</li> <li>building and exposing the links - it builds the links in its constructor and exposes them thanks to a base class that Spring Data provides.</li> </ul> <p>Here is the wrapper:</p> <pre class="java code java" style="font-family:inherit"><span style="color: #7F0055; font-weight: bold;">public</span> <span style="color: #7F0055; font-weight: bold;">class</span> PageResource<span style="color: #000000;">&lt;</span>T<span style="color: #000000;">&gt;</span> <span style="color: #7F0055; font-weight: bold;">extends</span> ResourceSupport <span style="color: #7F0055; font-weight: bold;">implements</span> Page<span style="color: #000000;">&lt;</span>T<span style="color: #000000;">&gt;</span> <span style="color: #000000;">&#123;</span> &nbsp;   <span style="color: #7F0055; font-weight: bold;">private</span> <span style="color: #7F0055; font-weight: bold;">final</span> Page<span style="color: #000000;">&lt;</span>T<span style="color: #000000;">&gt;</span> page; &nbsp;   <span style="color: #7F0055; font-weight: bold;">public</span> PageResource<span style="color: #000000;">&#40;</span>Page<span style="color: #000000;">&lt;</span>T<span style="color: #000000;">&gt;</span> page, <span style="color: #000000;">String</span> pageParam,       <span style="color: #000000;">String</span> sizeParam<span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span>     <span style="color: #7F0055; font-weight: bold;">super</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>;     <span style="color: #7F0055; font-weight: bold;">this</span>.<span style="color: #000000;">page</span> = page;     addPreviousLink<span style="color: #000000;">&#40;</span>page, pageParam, sizeParam<span style="color: #000000;">&#41;</span>;     addNextLink<span style="color: #000000;">&#40;</span>page, pageParam, sizeParam<span style="color: #000000;">&#41;</span>;     addFirstLink<span style="color: #000000;">&#40;</span>page, pageParam, sizeParam<span style="color: #000000;">&#41;</span>;     addLastLink<span style="color: #000000;">&#40;</span>page, pageParam, sizeParam<span style="color: #000000;">&#41;</span>;     addSelfLink<span style="color: #000000;">&#40;</span>page, pageParam, sizeParam<span style="color: #000000;">&#41;</span>;   <span style="color: #000000;">&#125;</span> &nbsp;   <span style="color: #7F0055; font-weight: bold;">private</span> <span style="color: #7F0055; font-weight: bold;">void</span> addPreviousLink<span style="color: #000000;">&#40;</span>Page<span style="color: #000000;">&lt;</span>T<span style="color: #000000;">&gt;</span> page, <span style="color: #000000;">String</span> pageParam,       <span style="color: #000000;">String</span> sizeParam<span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span>     <span style="color: #7F0055;font-weight: bold;">if</span><span style="color: #000000;">&#40;</span>page.<span style="color: #000000;">hasPreviousPage</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span><span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span>       Link link = buildPageLink<span style="color: #000000;">&#40;</span>pageParam,page.<span style="color: #000000;">getNumber</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>-<span style="color: #cc66cc;">1</span>,sizeParam,page.<span style="color: #000000;">getSize</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>,Link.<span styl
e="color: #000000;">REL_PREVIOUS</span><span style="color: #000000;">&#41;</span>;       add<span style="color: #000000;">&#40;</span>link<span style="color: #000000;">&#41;</span>;     <span style="color: #000000;">&#125;</span>   <span style="color: #000000;">&#125;</span> &nbsp;   <span style="color: #7F0055; font-weight: bold;">private</span> Link buildPageLink<span style="color: #000000;">&#40;</span><span style="color: #000000;">String</span> pageParam,<span style="color: #7F0055; font-weight: bold;">int</span> page,<span style="color: #000000;">String</span> sizeParam,<span style="color: #7F0055; font-weight: bold;">int</span> size,<span style="color: #000000;">String</span> rel<span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span>     <span style="color: #000000;">String</span> path = createBuilder<span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>         .<span style="color: #000000;">queryParam</span><span style="color: #000000;">&#40;</span>pageParam,page<span style="color: #000000;">&#41;</span>         .<span style="color: #000000;">queryParam</span><span style="color: #000000;">&#40;</span>sizeParam,size<span style="color: #000000;">&#41;</span>         .<span style="color: #000000;">build</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>         .<span style="color: #000000;">toUriString</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>;     Link link = <span style="color: #7F0055; font-weight: bold;">new</span> Link<span style="color: #000000;">&#40;</span>path,rel<span style="color: #000000;">&#41;</span>;     <span style="color: #7F0055; font-weight: bold;">return</span> link;   <span style="color: #000000;">&#125;</span> &nbsp;   <span style="color: #7F0055; font-weight: bold;">private</span> ServletUriComponentsBuilder createBuilder<span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span>     <span style="color: #7F0055; font-weight: bold;">return</span> ServletUriComponentsBuilder.<span style="color: #000000;">fromCurrentRequestUri</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>;   <span style="color: #000000;">&#125;</span> &nbsp;   <span style="color: #000000;">&#40;</span>...<span style="color: #000000;">&#41;</span> <span style="color: #808080; font-style: italic;">// other links method</span> &nbsp;   <span style="color: #808080; font-style: italic;">// Page&lt;T&gt; interface methods</span> &nbsp;   @Override   <span style="color: #7F0055; font-weight: bold;">public</span> <span style="color: #7F0055; font-weight: bold;">int</span> getNumber<span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span>     <span style="color: #7F0055; font-weight: bold;">return</span> page.<span style="color: #000000;">getNumber</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>;   <span style="color: #000000;">&#125;</span> &nbsp;   @Override   <span style="color: #7F0055; font-weight: bold;">public</span> <span style="color: #7F0055; font-weight: bold;">int</span> getSize<span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span>     <span style="color: #7F0055; font-weight: bold;">return</span> page.<span style="color: #000000;">getSize</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>;   <span style="color: #000000;">&#125;</span> &nbsp;   <span style="color: #000000;">&#40;</span>...<span style="color: #000000;">&#41;</span> other Page<span style="color: #000000;">&lt;</span>T<span style="color: #000000;">&gt;</span> methods delegate to the delegate &nbsp; <span style="color: #000000;">&#125;</span></pre> <p>I left only the parts that matter. There are different links but they're all built in the same way. I use Spring MVC's <code>ServletUriComponentsBuilder</code> to build the URL from the current request, with the appropriate HTTP parameters for the paging, and then create a Spring HATEOAS <code>Link</code> from the URL. The <code>ServletUriComponentsBuilder</code> is handy when it comes to build URL, as it handles path variables, expanding of the path template with values, and encoding.</p> <h2>New version of the controller</h2> <p>The controller doesn't change much, it just needs to build a <code>PageResource</code> from the page and return it:</p> <pre class="java code java" style="font-family:inherit">@Controller @RequestMapping<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;/contacts&quot;</span><span style="color: #000000;">&#41;</span> <span style="color: #7F0055; font-weight: bold;">public</span> <span style="color: #7F0055; font-weight: bold;">class</span> ContactController <span style="color: #000000;">&#123;</span> &nbsp;   @Autowired ContactRepository contactRepository;   @RequestMapping<span style="color: #000000;">&#40;</span>value=<span style="color: #888888;">&quot;/pages&quot;</span>,method=RequestMethod.<span style="color: #000000;">GET</span><span style="color: #000000;">&#41;</span>   @ResponseBody   <span style="color: #7F0055; font-weight: bold;">public</span> PageResource<span style="color: #000000;">&lt;</span>Contact<span style="color: #000000;">&gt;</span> contactsPages<span style="color: #000000;">&#40;</span>@RequestParam <span style="color: #7F0055; font-weight: bold;">int</span> page,@RequestParam <span style="color: #7F0055; font-weight: bold;">int</span> size<span style="color: #000000;">&#41;</span> <span style="color: #000000;">&#123;</span>     <span style="color: #000000;">Pageable</span> pageable = <span style="color: #7F0055; font-weight: bold;">new</span> PageRequest<span style="color: #000000;">&#40;</span>       page,size,<span style="color: #7F0055; font-weight: bold;">new</span> Sort<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;id&quot;</span><span style="color: #000000;">&#41;</span>     <span style="color: #000000;">&#41;</span>;     Page<span style="color: #000000;">&lt;</span>Contact<span style="color: #000000;">&gt;</span> pageResult = contactRepository.<span style="color: #000000;">findAll</span><span style="color: #000000;">&#40;</span>pageable<span style="color: #000000;">&#41;</span>;     <span style="color: #7F0055; font-weight: bold;">return</span> <span style="color: #7F0055; font-weight: bold;">new</span> PageResource<span style="color: #000000;">&lt;</span>Contact<span style="color: #000000;">&gt;</span><span style="color: #000000;">&#40;</span>pageResult,<span style="color: #888888;">&quot;page&quot;</span>,<span style="color: #888888;">&quot;size&quot;</span><span style="color: #000000;">&#41;</span>;   <span style="color: #000000;">&#125;</span> <span style="color: #000000;">&#125;</span></pre> <p>Here is how the JSON representation looks like now:</p> <pre> {    &quot;content&quot;:[       {          &quot;id&quot;:1,          &quot;firstname&quot;:&quot;Joe&quot;,          &quot;lastname&quot;:&quot;Dalton&quot;,          &quot;age&quot;:37       },       {          &quot;id&quot;:2,          &quot;firstname&quot;:&quot;William&quot;,          &quot;lastname&quot;:&quot;Dalton&quot;,          &quot;age&quot;:35       },       ... more records ...    ],    &quot;size&quot;:5,    &quot;number&quot;:0,    &quot;sort&quot;:[       {          &quot;direction&quot;:&quot;ASC&quot;,          &quot;property&quot;:&quot;id&quot;,          &quot;ascending&quot;:true       }    ],    &quot;totalPages&quot;:3,    &quot;numberOfElements&quot;:5,    &quot;totalElements&quot;:12,    &quot;firstPage&quot;:true,    &quot;lastPage&quot;:false,    &quot;id&quot;:{       &quot;rel&quot;:&quot;self&quot;,       &quot;href&quot;:&quot;http://localhost:8080/hateoas/zen-contact/contacts/pages?page=0&amp;size=5&quot;    },    &quot;links&quot;:[       {          &quot;rel&quot;:&quot;next&quot;,          &quot;href&quot;:&quot;http://localhost:8080/hate
oas/zen-contact/contacts/pages?page=1&amp;size=5&quot;       },       {          &quot;rel&quot;:&quot;first&quot;,          &quot;href&quot;:&quot;http://localhost:8080/hateoas/zen-contact/contacts/pages?page=0&amp;size=5&quot;       },       {          &quot;rel&quot;:&quot;last&quot;,          &quot;href&quot;:&quot;http://localhost:8080/hateoas/zen-contact/contacts/pages?page=2&amp;size=5&quot;       },       {          &quot;rel&quot;:&quot;self&quot;,          &quot;href&quot;:&quot;http://localhost:8080/hateoas/zen-contact/contacts/pages?page=0&amp;size=5&quot;       }    ] } </pre> <p>Notice the <code>id</code> and the <code>links</code> fields. They come from the <code>ResourceSupport</code> our <code>PageResource</code> class extends.</p> <p>A client of our REST web service is now able to navigate through the pages of our list of contacts. This client could be your favorite AJAX library, like JQuery, that would hit the web service and nicely formats the data in an HTML interface.</p> <h2>Conclusion</h2> <p>This post illustrated how to make a REST web service easier to use from the client's point of view: result pages contain links to navigate through the dataset. The implementation has been made straightforward thanks to the use of Spring Data JPA, Spring MVC, and Spring HATEOAS.</p> <p><a href="https://github.com/acogoluegnes/hateoas">Source code</a></p>