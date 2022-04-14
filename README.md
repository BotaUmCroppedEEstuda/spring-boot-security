<h1 align="center">spring-boot-security</h1>
<p align="center">Nesse documento vamos aprender a criar aplicação spring boot com autenticação.</p>

### Pré-requisitos:

- Baixar um projeto com as configurações padrão no [start.spring](https://start.spring.io/)

- Nesse exemplo, vamos seguir o passo-a-passo [howtodoinjava](https://howtodoinjava.com/spring-boot2/security-rest-basic-auth-example/)

<h2 align="center">LIBS NECESSÁRIAS</h2>

Nesse primeiro exemplo, vamos usar _spring-boot-starter-web_, _spring-boot-starter-security_ e _lombok_

```bash
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.22</version>
    <scope>provided</scope>
</dependency>
```

Importe no seu _pom.xml_ e rode no terminal:

```bash
$ maven clean install
```

<h2 align="center">BASIC AUTH</h2>

Vamos criar a classe:

{% gist c0541274bcfd2d4317a13067a6e333af %}
<script src="https://gist.github.com/tiiamati/c0541274bcfd2d4317a13067a6e333af.js"></script>

Entendendo as configs:
- **_@Configuration_**: annotation indicates that a class declares one or more @Bean methods and may be processed by the Spring container to generate bean definitions and service requests for those beans at runtime. Since spring 2, we were writing our bean configurations to xml files
- **_@Autowired_**: enables you to inject the object dependency implicitly. It internally uses setter or constructor injection. Autowiring can't be used to inject primitive and string values

**protected void configure(HttpSecurity http)**: Onde vamos configurar como será o tipo de autenticação que vamos aplicar.
- **_.csrf().disable()_**: Desabilita mecanismos de defesa, não é recomendado desabilitar, apenas para testes locais, ou para serviços que seram utilizados apenas internamente.
- **_.authorizeRequests()_**: Onde permitimos que existe uma restrição nas requisições dos endpoints.
- **_.antMatchers("/teste/\*\*").permitAll()_**: Permite qualquer requisição que atenda a essa combinação.
- **_.anyRequest().authenticated()_**: Indica que qualquer endpoint exposto necessita de autenticação.
- **_and()_**: Sem esse and, não é possível passar o tipo de autenticação que vamos implementar, nosso caso: httpBasic.
- **_.httpBasic()_**: É o tipo de autenticação que vamos solicitar do requisitante.

**public void configureGlobal(AuthenticationManagerBuilder auth)**: Onde vamos configurar o usuário e senha que permitimos se autenticar na aplicação.
- **_auth.inMemoryAuthentication()_**: Guardamos essa configuração em memoria.
- **_.withUser(basicAuthConfig.getUsername())_**: Onde setamos o usuário.
- **_.password("{noop}".concat(basicAuthConfig.getPassword()))_**: Onde setamos a senha.
- **_.roles("USER")_**: Tipo de permissão do usuário.

Vamos criar a classe:

<script src="https://gist.github.com/tiiamati/2c3951cadbaf53972b850173a77dd3e6.js"></script>

Entendendo as configs:

- **_@Data_**: Equivalent to @Getter @Setter @RequiredArgsConstructor @ToString @EqualsAndHashCode.
- **_@Component_**: is an annotation that allows Spring to automatically detect our custom beans. In other words, without having to write any explicit code, Spring will: Scan our application for classes annotated with @Component. Instantiate them and inject any specified dependencies into them. Inject them wherever needed.
- **_@ConfigurationProperties_**: is letting developer maps the entire . properties and yml file into an object easily. 
- **_(prefix = "security")_**: A palavra security indica a "arvore" que queremos mapear dentro do arquivo **application.yaml**.

Altere seu application.yaml:

```bash
security:
  username: usuario
  password: senha
```

<h2 align="center">CONTROLLER</h2>

Agora vamos expor um endpoint para testar nossa autenticação.

Vamos criar a classe:

<script src="https://gist.github.com/tiiamati/32d1eb97698de9d5092da731850fa1d3.js"></script>

Criamos dois endpoints:
- http://localhost:8080/{name}
- http://localhost:8080/teste/{name}

O endpoint que contem a string "teste" não precisa estar autenticado, pois na nossa config nós indicamos que esse "matchers" está como "permitAll", já o outro endpoint deve estar autenticado para ser acessado.

<h2 align="center">TESTANDO</h2>

Estamos rodando o projeto na porta 8080, sendo assim, abra o postman e tente acessar **http://localhost:8080/hoje** sem passar nenhum tipo de autenticação:

<img src="https://i.picasion.com/pic92/4d08a17c4f96ef15e17983320f42db1c.gif" width="300" height="199" border="0" alt="https://picasion.com/" /></a><br /><a href="https://picasion.com/">

Você vai tomar um erro 401 Unauthorized. 

Agora, selecione a autenticação _Basic Auth_, preencha com os valores que você setou no arquivo **application.yaml** e envie a requisição novamente:

<img src="https://i.picasion.com/pic92/45336db98e5b2ba58a9795ce01f1bf6a.gif" width="300" height="199" border="0" alt="https://picasion.com/" /></a><br /><a href="https://picasion.com/">
 
Vamos remover a autenticação, e fazer uma requisição no endpoint **http://localhost:8080/teste/hoje**

<img src="https://i.picasion.com/pic92/1f306b284cbb41498b6cfaf51441e306.gif" width="300" height="198" border="0" alt="https://picasion.com/" /></a><br /><a href="https://picasion.com/">