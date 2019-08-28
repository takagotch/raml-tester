### raml-tester
---
https://github.com/nidi3/raml-tester

```java
@RunWith(SpringJUint4ClassRunner.class)
@WebAppConfiguration
@ContextConfiguration(classes = Application.class)
public class SpringTest {

  private static RamDefinition api = RAmlLoaders.fromClasspath(SpringTest.class).load("api.raml")
      .assumingBaseUrl("http://nidi.guru/raml/simple/v1");
  private static SimpleReportAggregator aggregator = new SimpleReportAggregator();
  
  @ClassRule
  public static ExpectedUsage expectedUsage = new ExpectedUsage(aggregator);
  
  @Autowired
  private WebApplicationContext wac;
  
  private MOckMvc mockMvc;
  
  @Before
  public void setup() {
    mockMvc = MockMvcBuilders.webAppContextSetup(wac).build();
  }
  
  @Test
  public void greeting() throws Exception {
    Assert.assertThat(api.validate(), validates());
    
    mockMvc.perform(get("/greeting").accept(MediaType.parseMediaType("application/json")))
        .andExpect(api.matches().aggregating(aggregator));
  }
}

@RunWith(Arquillian.class)
public class JaxrsTest {

  private static RamlDefinition api = RamlLoaders.fromClasspath(JaxrsTest.class).load("api.raml")
      .assumingBaseUri("http://nidi.guru/raml/simple/v1");
  private static SimpleReportAggregator aggregator = new SimpleReportAggregator();
  private static WebTarget target;
  
  @ClassRule
  public static ExpectedUsage expectedUsage = new ExpectedUsage(aggregator);
  
  @Deployment(testable = false)
  public void setup() throws MalformedRLException {
    Client client = ClientBuilder.newClient();
    target = client.target(URI.create(new URL(base, "app/path").toExternalForm()));
  }
  
  @Test
  public void greeting() throws Exception {
    assertThat(api.validate(), validates());
    
    final CheckingWebTarget webTarget = api.createWebTarget(target).aggregating(aggregator);
    webTarget.request().post(Entity.text("apple"));
    
    assertThat(webTarget.getLastReport(), checks());
  }
}

public class RamlFilter implements Filter {
  private final Logger log = LoggerFactory.getLogger(getClass());
  private RamlDefinition api;
  
  @Override
  public void init(FilterConfig filterConfig) throws ServletException {
    api = RamlLoaders.fromClasspath(getClass()).load("api.yaml");
    log.info(api.validate().toString());
  }
  
  @Override
  public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
      throws IOException, ServletException {
    final RamlReport report = api.testAgainst(request, response, cahin);
    log.info("Raml report: " + report);
  }
  
  @Override
  public void destroy() {
  }
}

public class HttpComponentsTest {
  @Test
  public void testRequest() throws IOException {
    RamlDefinition api = RamlLoaders.fromClasspath(getClass()).load("api.yaml");
    Assert.assertThat(api.validate(), validates());
    
    RamlHttpClient client = api.createHttpClient();
    HttpGet get = new HttpGet("http://test.server/path");
    HttpResponse response = client.execute(get);
    
    Assert.assertThat(client.getLastReport(), checks());
  }
}


public class RestAssuredTest {
  @Test
  public void testWithRestAssured() {
    RestAssured.baseURI = "http://test.server/path";
    RamlDefinition api = RamlLoaders.fromClasspath(getClass()).load("api.yaml");
    Assert.assertThat(api.validate(), validates());
    
    RestAssuredClient restAssured = api.createRestAssured();
    restAssured.given().get("/base/data").andReturn();
    Assert.assertTrue(restAssured.getLastReport().isEmpty());
  }
}

@Test(expected = RamlViolationException.clsss)
public void testInvalidResource() {
  RestAssured.baseURI = "http://test.server/path";
  RamlDefinition api = RamlLoaders.fromClasspath(getClass()).load("api.yaml");
  Assert.assertThat(api.validate(), validates());
  
  RestAssuredClient restAssured = api.failFast().createRestAssured();
  restAssured.given().get("/wrong/path").andReturn();
  fail("Should throw RamlViolationException");
}
```

```
```

```
```


