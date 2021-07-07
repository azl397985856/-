# 1. 前后端分离

## 前后端分离的历史

软件开发早期是没有前后端分离的概念的，为什么呢？因为当时压根儿就没有前端工程师，专门的前端工程师大约是在 2005 年。在此之前前端是不受重视的。这其实和软件的发展有关。说到这里又不得不提到 js 的发展历史。JavaScript 诞生于 1995 年。起初它的主要目的是处理以前由服务器端负责的一些表单验证。后来大家对页面的要求越来越高，js 又给 web 多了一些动态功能。大家对前端的需求就是展示静态内容或者简单的动态内容（比如 CGI 返回数据拼接输出“动态”内容）。不过这种直接通过 CGI 或者 servlet 输出 HTML 的方法会使得前后端严重耦合，修改 HTML 需要改后端代码。为了将数据和模板分离，出现了很多动态拼接的技术，ASP，JSP，ASP.net 就是那个时期的产物。但是这种方式还是有问题，因此这种方式数据严重依赖于请求流，它是将请求流解析，然后将响应流输出到模板作为模板数据解析出 HTML 返回到后端，另外后端需要懂得前端的知识，HTML 语法，JS 语法，甚至要自己写 CSS，这个时期也是 MVC 盛行的时期，这个时期的 View 是放在后端的。

1998 年 ajax 技术的出现，允许客户端脚本发送 HTTP 请求（XMLHTTP），并且局部刷新页面，这种突破性的创新使得 web 高速发展，推动了 web 的发展。随着 HTML5，CSS3，ES6（简称 356）的出现，web 正以前所未有的速度前进，web 工程师从无到有，再到现在 web 工程师被赋予了很多花环，机遇和挑战。也因此前后端不得不逐渐的分离。

这之后又出现了将数据和模板完全分离的技术如 velocity 等，写法上完全改变了。 下面是一个 velocity 的 demo：

```java
import java.io.StringWriter;
import org.apache.velocity.app.VelocityEngine;
import org.apache.velocity.Template;
import org.apache.velocity.VelocityContext;
public class  Velocity {
    public static void main( String[] args )  throws Exception   {

    VelocityEngine ve = new VelocityEngine();
    ve.init();

    Template t = ve.getTemplate( "hellosite.vm" );

    VelocityContext context = new VelocityContext();
    context.put("name", "Eiffel Qiu");
    context.put("site", "http://www.eiffelqiu.com");

    StringWriter writer = new StringWriter();
    t.merge( context, writer );

    System.out.println( writer.toString() );
    }
}
```

> 这种思想是一大创新，包括 react 的渲染都是这样的思路，react 的渲染方式是 render,本质上开发者写的 jsx 就是 template，app 的 state 和 props 就是 context，然后根据纯函数的特性，生成 view。

虽然这是一个重大创新，不过它是严重依赖语言实现的，我们从上面的例子就可以看出来。那么如何使用一种通用的模板引擎？答案就是将模板放到前端，将模板这部分工作交给前端，这个时候逐渐又了前后端分离的概念。说到这里，不得不提到 2010 年 facebook 工程师提出的 bigpipe。他将页面渲染分为八个关键阶段，每一个阶段叫做 pagelet。先输出页面主体到前端，也就是前面提到的 Template，然后前端 ajax 请求数据，将请求的数据在前端进行拼接，这种方式比后端拼接，优点就是用户可以看到一个渐进式的过程，这在当时是一个巨大创新。缺点就是浪费若干 RTT（多了若干请求）。但是经过测试，在大多数浏览器中，BigPipe 都能将用户感受到的延迟时间降低一半，除了 Firefox 3.6，BigPipe 可以将 Firefox 3.6 的延迟时间降低 50ms 左右，大约降低了 22%左右。

然后开发者将模板引擎放到了前端，后端只是放一个 HTML 的空壳，代表作是 react。这个时候模板解析的任务是前端，后端完全从模板的地狱中逃了出来。但是还是有问题，就是用户看到的页面会是一个空壳，并不是一个 app shell。因此出现了 ssr 等技术，本质上就是回到之前的渲染方式，区别在于目前不是将所有的逻辑都交给后端，而是后端只处理首屏内容。

## 前后端合并

正所谓合久必分，分久必合。 前几年被热炒的全栈工程师，以及前后端同构技术都反映了前后端在分离之后又逐渐合并的迹象。为什么会这样呢？前后端分离虽然减少了后端开发的工作（最初前端都是后端写的，比如.net 的 aspx java 的 jsp 等）。但前后端分离地不干净导致一些沟通问题反而不如一个人来做。为了解决这个问题，主要又两种方式：

- 全栈工程师。 将所有工作交给一个人，或者有着前后端知识的一群人，这样沟通起来成本比较低。
- 稍后讲。

  **那还又必要分离吗？**

  上面讲述了前后端合并，那么还有必要分离吗？ 非常有必要！！！

刚才说为了解决前后端沟通问题主要又两种方式，下面说下第二种。

- 建立中间层。 前后端问题产生的原因主要是两个，一个是知识背景，技术栈不同，难以互相理解。二是 前后端是一个依赖的关系，前端需要依赖后端的数据接口，因此存在工作上的先后关系。 建立中间层可以有效减少上述的问题。 目前淘宝，挖财，51，有赞，二维火都在尝试这种方式。这也是我将会在后面重点描述的方式。

最近出现的 docker 容器技术等有效地减少了后端的工作量，让后端更加专注业务逻辑的编写。我觉得 node 的出现也大概如此吧，它的出现不是用来替代 apache 成为下一个 web 容器，我觉得他更是扩展了前端的领域，使得可以将一部分后端无法做（比如 ssr）或者不好做（不愿意做）的东西（比如接口聚合），移交给前端。

> 我不喜欢被冠以前端工程师，后端工程师的帽子。 我喜欢工程师这个称呼，我觉得我是以工程化的方式解决问题的角色，而不应该限定于某一部分职责。这个我主张前后端分离不矛盾，每个角色都自己的分工，都有自己精细化的一面。我们不试图取代后端，我们只是专注做好自己罢了。

## 前后端耦合

我有幸在我的职业生涯中经历了前后端耦合，前后端开发分离，部署分离的“分离阶段”， 以及大厂（alibaba）在这些方面的解决方案，所以非常荣幸能够将自己的经验和心得与大家分享。

最初在百世做前端的时候，我经历了前后端极度耦合的方式前后端虽然由不同的人来书写，那恰好也是我的领导首次尝试前后端分离模式，在此之前前后端通常都是一名后端人员来写的。这种模式虽然将前后端工作进行了切分，但是其耦合的成分还是非常大。

首先我们来看下我们的具体做法。 1. 前后端约定接口。 2. 前端根据后端接口进行开发。 3. 后端进行开发。（2 与 3 基本并行） 4. 前端打包代码，并将代码发送给后端。后端放到 war 包下。

tomcat/conf/web.xml 配置如下：

```markup
<welcome-file-list>
<welcome-file>index.html</welcome-file>
<welcome-file>index.htm</welcome-file>
<welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

将 war 文件直接复制到 tomcat/webapps 下

我们后端使用的是 java，具体做法：

- 如果是 Intellij Idea,在导入前端项目之后，右键项目 add framework support --&gt; web application，这时将会把前端项目转换为一个 javaweb 项目，然后将静态资源放在生成的 web 目录下即可。
- 如果是 eclipse，可以新建一个 javaweb 项目然后将静态资源放入 web 或者 webcontent 目录下，或者直接先导入前端项目，然后通过 project facts 将项目转换为 dynamic web 项目并勾选 js 等相关配置。

  然后，运行项目时把后端的 war 包和前端的 war 包一同添加到 deployment 中运行即可

这一阶段我们实现了初步的前后端分离。 但是上述做法有两个问题。 1. 由于上述的做法存在严重的问题在于前端对于发布控制力明显不足，比如版本控制不好做。 另外由于发布通常需要两个编译环境，即 jdk 编译后端代码，node 环境编译前端代码。前端通常需要安装后端环境如 jdk，后端也需要安装前端环境如 node。不管是学习成本还是沟通成本都是一个问题。

1. 前端通常需要等待后端给出测试数据后才能开工。所以我上面说“基本并行”

由于存在上面的两个问题，我们进一步探索出了下面的前后端”分离“模式。

## 前后端“分离”

这一阶段我们通过 nginx 将前后端部署分离，通过 mock 服务器将前后端时间上的耦合给减少了。 下面是我们的具体做法： 1. 前后端约定接口 2. 前端开发 3. 后端开发（2 和 3 完全并行） 4. 前端打包，并将代码通过 ftp 上传到文件服务器。 5. 配置 nginx 将静态请求代理到上面发布文件的文件服务器，后台接口代理到 apache web server。

和上面不同的地方，我们只需要将代码上传到文件服务器或者 CDN 上。 通过 nginx 转发控制即可。 避免了上面必须同时安装后端和前端编译环境的繁琐。

nginx 配置 通常是这样的：

```markup
server {
        listen       80;
        server_name  example.com;

        charset utf-8;

        #access_log  logs/host.access.log  main;

        location / {
              proxy_pass http://tomcat_pool;
              proxy_redirect off;
              proxy_set_header HOST $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              client_max_body_size 10m;
              client_body_buffer_size 128k;
              proxy_connect_timeout 90;
              proxy_send_timeout 90;
              proxy_read_timeout 90;
              proxy_buffer_size 4k;
              proxy_buffers 4 32k;
              proxy_busy_buffers_size 64k;
              proxy_temp_file_write_size 64k;
        }

        location ~ .*\.(html|htm|gif|jpg|jpeg|bmp|png|ico|txt|js|css|woff|woff2|ttf|eot|map)$  {
             root D:\Workspaces\front-end;
             index index.html;
        }
```

## 大厂的方案

这里主要介绍下阿里巴巴-钉钉的前后端分离解决方案。阿里这样的大厂通常有着自己内部的成熟系统支撑业务，比如阿里的持续集成系统 aone，配置服务 diamond，分布式服务系统 hsf 等等。 小公司通常没有经济实力去搭建自己的内部系统，我有幸接触到了这些业界较为顶级的系统，对它们的运作方式有了一定的了解。这里简单介绍下阿里巴巴-钉钉的前后端分离解决方案。 钉钉的具体做法如下：

1. 前后端约定接口
2. 约定接口上 dip（一个 mock 服务器）
3. 前端开发
4. 后端开发（2 和 3 完全并行）
5. 前端打包，push 触发测试环境和预发环境的云构建，

   打 tag 触发线上环境云构建自动发布静态资源到 cdn。

6. 需要发布只需要负责人修改 diamond 配置即可，版本控制变得简单。

可以发现这种方式其实就是比上面多了两个点，一是云构建自动化，二是 diamond 配置。 云构建其实属于自动化，我将在流程自动化里面详细描述，云构建本身和 前后端分离没有关系。 其实两者的区别在于 diamond 配置。那么为什么配置 diamond 是前后端分离的工作呢？作为一个配置服务，diamond 将专业性比较强的东西抽离出来，将包括但不限于版本管理的内容可以交给没有任何前端背景的人来做。

## 前后端分离的理想方式

上面的这种方法是非常高效的一种方式，但仍然不是我认为的理想的前后端分离方式。 我认为理想的前后端分离方式是**职能上**，后端提供纯粹的接口，**只需要提供数据**-系统的数据或者根据根据二方库获取数据返回前端，剩下的逻辑前端做。**时间上**，前后端可以并行开发，就像下面这张图一样。

![图4.04](https://raw.github.com/azl397985856/automate-everything/master/illustrations/图4.04.png)

前后端的约定就像 HTML 一样，是前置资源，是前提。当这个前置资源定了之后，前端（就像上图的 CSS）和后端（就像上图的 JS）可以并行加载，它们中全部完成了，页面就可以加载完毕（这里不考虑其他外部资源）。

职能上由于后端提供元数据，前端只需要组合，前后端在逻辑和时间上没有了耦合。先来一张图来描述下： ![图1.1](https://raw.github.com/azl397985856/automate-everything/master/illustrations/图1.1.png)

如图，后端只是提供原子数据，保证数据稳定输出就可以了，事实上保证系统稳定很多已经是运维在做的事了。前端需要根据需要进行接口整合，服务端渲染，mock 数据等工作。 那么整个流程具体是怎么工作的呢？ 可以下面这张图： ![图1.2](https://raw.github.com/azl397985856/automate-everything/master/illustrations/图1.2.png)

可以看出请求首先留到 ngxin（反向代理），nginx 判断是否是静态请求（html），如果是则转发到 node 服务器，node 服务器会判断是否需要进行 ssr，如果需要则调用后台接口拼装 html，将**html 和应用状态**一起返回给前端。 如果不需要 ssr，则直接返回静态资源，并设置缓存信息。 如果不是静态资源，判断头部信息（比如有一个自定义字段 reselect: 'node' \| ''），是否需要请求合并，如果需要则请求到 node 端，如果不需要直接转发给后端服务器。 ngxin 配置大概是这样：

```markup
map $reselect/node $reselect {
  default "";
  "node" "reselect/node";
}

server {
  listen                    80;
  server_name               demo.io;

  charset                   utf-8;
  autoindex                 off;

  location / {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-Ssl on;

    if ($reselect="reselect/node"){
      proxy_pass      http://node-demo.io;
      break;
    }

    proxy_pass      http://java-demo.io;
  }
  location ~ .*\.(html|htm|gif|jpg|jpeg|bmp|png|ico|txt|js|css|woff|woff2|ttf|eot|map)$  {
       root D:\Workspaces\front-end;
       index index.html;
  }
}
```

中间层可以做的事情非常多，除了我刚才说的服务端渲染，接口组合，mock 数据，还可以做很多别的事。我在这里不会讲述 ssr，接口组合，mock 如何具体做，因为这不是本章的重点，而且也有很多最佳实践，我要说的是如何将这些“最佳实践“ 组合起来，如何在我们工作中将其应用起来，并且具有良好的扩展性。 那么中间层拿到请求具体流程上面已经讲过了，现在我们以实战的角度，讲下代码该怎么组织。 但是为了方便大家理解，我简单介绍下。 首先是服务端渲染，我以 react+redux 做服务端渲染讲解，为了简单起见，没有引入 react-router，大家直接看代码理解：

服务端：

```markup
<body>
    <div id="container"><%- html %></div>
    <script>
        window.__INITIAL_STATE__ = <%- JSON.stringify(initState) %>
    </script>
</body>
```

```javascript
const store = buildStore(rootReducer, {});
Promise.all([
  store.dispatch(fetchUserInfo()),
  store.dispatch(fetchPosts())
]).then(() => {
  const html = renderToString(
    <Provider store={store}>
      <RouterContext {...renderProps} />
    </Provider>
  );
  const initState = store.getState();
  res.render("index", html, initState);
});
```

客户端：

```javascript
const initState = window.__INITIAL_STATE__;
const store = storeApp(initState);
ReactDOM.render(
  <Provider store={store}>
    <Router history={browserHistory}>{routesApp}</Router>
  </Provider>,
  document.getElementById("container")
);
```

上面这是对服务端渲染的一个极简实现，那么接口聚合呢？mock 呢？ 加入其他功能呢？ 是不是对我们 express 本身侵入性太大。 在这里我借鉴了微服务的理念，同时利用第二章节要讲模块化的思想，组织了中间层。 那么究竟我是怎么设计的呢，请继续往下看。

![图1.3](https://raw.github.com/azl397985856/automate-everything/master/illustrations/图1.3.png)

我们的关注点就是服务集群，如果需要增加集群就直接修改配置即可。 下面基于 docker + docker-compose + node + nginx 做一个**中间层系统**。

docker-compose.yml

```text
version: '2'

services:


    nginx:
        build: ./nginx
        container_name: ms_nginx
        links:
            - posts
            - users
        ports:
            - "80:80"

    api:
        build: ./api
        container_name: ms_posts
        environment:
            - loglevel=none

        volumes:
            - "./posts:/src/app"
        working_dir: "/src/app"
        ports:
            - "8080:8080"
            - "5858:5858"
        # command: npm run start
        command: npm run start:dev

    sever-render:
        build: ./sever-render
        container_name: ms_sever-render

        volumes:
            - "./users:/src/app"
        working_dir: "/src/app"
        command: npm start
```

nginx 的配置

```markup
worker_processes 4;

events { worker_connections 1024; }

http {

    server {
          listen 80;
          charset utf-8;

          location / {
            proxy_pass http://server-render:8080;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
          }

          location ~ ^/api {
            rewrite ^/api/(.*) /$1 break;
            proxy_pass http://users:8080;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
          }
    }
}
```

这样我们实现了不同的系统分流，并且彼此之间解除耦合，通过 nginx 去 分发。这样还存在一个问题就是样板代码比较多， 有没有一种方法，让我们只关注业务本身，而不需要样板代码呢？大家可以关注下 [faas](https://github.com/openfaas/faas), 这里不在赘述。 当我们的业务逐渐复杂，系统逐渐增多，域名逐渐增多，会发现很多东西都在 nginx 中，这时候需要将配置分开，每一个子业务一个配置文件。

1. 在 nginx 安装目前下 新建 vhost 文件夹
2. 在文件夹下创建 \*.conf 的文件 ，如 a.example.com.conf ，命名规则大多以域名的方法来命名文件。
3. 辑 conf 文件，把我们平常放在 nginx.conf 里的 server{...} 段复制过来直接粘贴到 conf 里。

   在 nginx.conf 的 http{...} 段中加入 include E:/nginx-1.8.1/vhosts/.conf;

> 注：这里 include 需要用到全路径，且文件后缀是用 conf\*\*

这里介绍一个淘宝开源软件 tengine，他是 nginx 的超集。有很多强大的功能，我之前的公司百世就是用的[tengine](http://tengine.taobao.org/)。

## 前后端分离的好处

前后端分离的一个好处就是将人员分工更加明确，前端只关注界面，后端只负责数据，从而使效率显著提高。 再有就是前后端分离可以使得后端接口复用性更强（前端的复用通常可以采取组件的方式），如果公司需要针对 同一产品开发多套 UI（原生，手机端，PC 端等），理论上如果后端接口设计地足够好，是不需要改变的。 前端只需要根据不同的 UI 设备的呈现能力（手机屏幕小，因此元素更加紧凑，简约），实现不同的交互方式。

前后端分离从技术角度上讲是后端技术和前端技术的解耦，从业务角度上讲，是业务逻辑和用户体验的解耦。

## 前后端分离的挑战

要实行前后端分离，不得不面对的一个问题就是前后端沟通问题。前面提到前后端分离，本质上是基于契约式的开发， 如果契约发生改变，那么就需要前后端再次进行沟通。不要小看沟通问题，沟通是软件开发环节中最难控制的一环。 如果是跨部门甚至跨公司沟通，沟通成本会显著上升。

在有一个问题就是接口设计，前面提到前后端分离的核心是契约式开发。契约本质上就是 API 接口，因此 API 接口的 设计直接影响了前后端分离的实现。目前 facebook 提出的 graphql 也非常火热，其本质上是应用层查询语言。解决的 问题就是 restfull 接口的数据，接口冗余的问题。而目前绝大多数的前后端分离 API 设计都是基于 restfull 或者类 restfull 的设计。如果感兴趣，可以自行查阅 graphql 的参考资料。

前后端分离之前，路由是放到后台的。如果做前后端分离，前端负责页面的路由，后端暴漏若干接口。这种方式相比 后台直出页面有一个问题就是，我们的接口是暴漏的，这就对安全问题提出了更高的要求。这部分不是本文的讨论重点， 不再赘述。

前后端分离之后，文档的重要性显得更加重要。这就要求有一个明确的文档化的产品落地，而不是存在于人的头脑之中。

## 总结

本章介绍了四种前后端分离的方式和阶段，这里需要强调的是并不是越往后的方式越好，问题的关键点还是选择合适的 方式，根据当前所处阶段选择合适的分离方式，提高单体和整体作战效率才是明智之举。
