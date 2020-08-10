# 基于webgis的外卖辅助决策系统模拟实现

## 绪  论
### 1.1  研究背景
由于Internet的爆炸性发展，Web服务正在进入千家万户，WebGIS给更多用户提供了使用GIS的机会。现在流行的WebGIS平台有: ARCIMS;Mapgis IMS;Super Map IS;TopMap World;MapXtreme等国内外成熟产品。WebGIS可以使用通用浏览器进行浏览、查询，也可通过浏览器上的插件(plug-in)、ActiveX控件和Java Applet来进行WebGIS功能的访问，浏览器和这些插件都是免费的，易获取的，很大程度上扩大了GIS的潜在用户范围[1]。而以往的GIS由于成本高和技术难度大，往往成为少数专家拥有的专业工具，很难推广[2]。
“互联网+”的发展，使得现在越来越多人开始喜欢网上订餐，饿了吗、美团等订餐平台的崛起催生了外卖员这一行业，也使得送餐不及时导致许多外卖员收到差评，不仅影响了自己的收入，也影响了商家的口碑。
### 1.2  研究意义
诚然，外卖配送不及时这个问题涉及到许多方面，如商家制作餐品的时间，外卖员配送路径的交通状况等等。好奇心驱使本人从多个城市多个平台从事一段时间的外卖配送工作，最终从必胜客宅急送那里得到一些灵感。他们从接单到出餐再到送餐一切都井然有序，尤其是他们的接单嵌入式系统能接受多个平台的订单，并将订单的信息以类似于表的形式实时显示在屏幕上，每一条都记录了客户的地址，等待时间以及距离，然后骑手轮流按这些订单排序，选择其中的一单或者几单进行配送。然后配送员记下自己需要配送的订单，再根据地图软件搜索这些地址，规划路径然后开始配送。他们的整个配送程序都显得很有效率，只是对于路不熟的配送员来说，打开手机搜索地图显得有点浪费时间。于是本人打算利用WebGIS与他们的订单系统结合起来，以地图的方式更加直观地显示用户订单信息，同时显示距离客户点的最短路径，让配送员一眼便知道往哪里送，怎么送，还有多少时间可以送，以此来帮助他们更好地决策，根据系统显示的信息，亦可自由规划路径，心中一张图直接上手去做。
然而，本人水平有限，此系统只是用屏幕点击事件来模拟了所有的请求，并没有实际的数据库设计和相关订单单接口的引入，仅以此例为以后的研究做样本。
## 2 相关技术介绍
### 2.1  WebGIS原理
顾名思义，WebGIS就是展现于网络上的GIS。在上世纪八十年开始，出现的大量GIS软件，基本上都是C/S架构的，也就是首先需要在本机上装有专业的GIS软件，然后用户根据需求使用这些GIS软件。当然这种桌面端的GIS软件有其天然的优势，比如开发相对简单，不需要Internet支持，以及可以做更多的复杂功能、响应相对迅速等优势。但是同时，其缺点更是不容忽视。首当其冲的便是不便于推广，而不便于推广的原因在于C/S系统其本身的天然的劣势：不便于更新，不便于跨平台，不便于用户在不安装指定客户端的情况下使用。
从本世纪开始，internet进入了爆发式增长阶段，网络的铺设以及网速的提升都有了大幅度增加，这为WebGIS的发展提供了坚实的大环境[3]。于是，基于B/S的GIS系统越来越多的开始提供服务，并且在RIA（富客户端）技术、AJAX（动态网页）技术的涌现和成熟，WebGIS也基本能展现出与C/S一样的效果和功能。而WebGIS的大发展，更是用户的需求，google地图和百度地图等服务提供商的大规模扩张便是最好的证明[4]。
什么是WebGIS，就是将GIS这门学科所能提供的功能，以B/S技术展现给用户，使用户只需要在浏览器上便能使用这些GIS功能的一个应用方向 。
WebGIS应用客户／服务器概念来执行GIS的分析任务[5]。它把任务分为服务器端和客户端两部分，客户可以从服务器请求数据、分析工具或模块，服务器或者执行客户的请求并把结果通过网络送回给客户，或者把数据和分析工具发送给客户供客户端使用。
　　早期的WebGIS只是简单地将固定的地图图片链接到网页上，对于所有的用户查询，系统返回的是预先制成的相同的地形文件和数据[6]。后来随着技术的进步出现CGI和APl技术和插件和空间技术，目前实现WebGIS可以有多种技术方法，主要有公共网关接口(ConlmonGatewayInterface，CGl)、服务器应用程序接口(ServerAPl)、插件法(plug—in)、ActiveX技术，以及Java编程等。WebGIS系统结构如下图所示。
![image](http://dlwxz.com/bs1.png)
图 1 webgis系统结构
### 2.2  ArcGIS Server
ArcGIS Server 允许以跨企业和跨 Web 网络的形式共享 GIS 资源[7]。
GIS 资源指的是要与其他人共享的地图、地球、地址定位器、地理数据库和各种工具。可通过将这些资源存放在 ArcGIS Server 系统或 GIS 服务器中并允许客户端应用程序使用这些资源和与这些资源进行交互来共享这些资源。
除了提供对特定 GIS 资源的访问之外，GIS 服务器还提供对此类资源所包含的 GIS 功能的访问。 GIS 服务器不但允许您共享资源（如地图），还允许您访问嵌入在资源中的 GIS 功能[8]。
    人们如何使用服务器上的 GIS 资源取决于他们的身份。一个 GIS 分析人员可以创作包含一个图层（该图层引用 GIS 服务器上的地图资源）的地图。一个开发人员可以构建一个使用地图和地址定位器安排运货卡车时间表和行车路线的 Web 应用程序。某个城市委员会的成员可在做出会影响他们所居住社区的决定之前通过 Web 应用程序使用一个地图（例如，查找适合于再开发的地点）[9]。
    ArcGIS Server是一个用于构建集中管理和支持多用户的企业级GIS应用平台，支持的GIS软件可以集中管理并且支持多用户。ArcGIS Server提供了创建和配置GIS应用程序和服务的框架，这样可以满足各种客户端的各种需求。通过ArvGIS Server可以实现对GIS提供基于浏览器的访问方式；在企业内部发布高级的GIS网络服务；应用.NET开发个性化应用程序，满足用户各种特殊的需求；应用行业标准软件集成GIS和其他IT技术；提供集中管理、多用户编辑的能力；在服务器上实现集中的空间分析。
### 2.3  ArcGIS API for JavaScript
#### 2.3.1 ArcGIS API for Javascript 介绍
ArcGIS API for Javascript是由美国Esri公司推出，跟随ArcGIS 9.3同时发布的，Esri基于dojo框架和REST风格实现的一套编程接口（目前最新版本为4.7）。
通过ArcGIS API for Javascript可以对ArcGIS for Server进行访问，并且将ArcGIS for Server提供的地图资源和其他资源（ArcGIS Online）嵌入到Web应用程序中[10]。
#### 2.3.2	ArcGIS API for Javascript 主要特点
空间数据展示：加载地图服务，影像服务,WMS 等。
客户端 Mashup：将来自不同服务器、不同类型的服务在客户端聚合后统一呈现给客户。
图形绘制：在地图上交互式地绘制查询范围或地理标记等。
符号渲染：提供对图形进行符号化,要素图层生成与题图和服务器端渲染等功能。
查询检索：基于属性和空间位置进行查询，支持关联查询，对查询结果的排序分组以及对属性数据的统计。
地理处理：调用 ArcGIS for Server发布的地理处理服务（GP服务），执行空间分析、地理处理或其他需要服务器端执行的工具、模型、运算等。
网络分析：计算最优路径、临近设施和服务区域。
在线编辑：通过要素服务编辑要素的图形、属性、附件,进行编辑追踪。
时态感知：展示、查询具有时间特征的地图服务或影像服务数据。
影像处理：提供动态镶嵌、实时栅格函数处理等功能。
地图输出：提供多种地图图片导出和服务器端打印等功能。
#### 2.3.3	Dojo介绍
Dojo是一个强大的面向对象的JavaScript框架，主要由三大模块组成：Core、Dijit、DojoX。其中Core提供Ajax、events、packaging、CSS-based querying、animations、JSON等相关操作API。Dijit是一个可更换皮肤，基于模板的WEB UI 
控件库。Dojox包括一些创新、新颖的代码和控件：DateGrid、charts、离线应用、跨浏览器矢量绘图等[11]。
Dojo的特点可从以下几部分说起：
Dojo是一个纯JavaScript库，后台只要提供相应的接口就能够将数据以Json格式输出给前台。
Dojo自身定义了完整的函数库，屏蔽了浏览器的差异。
Dojo自身定义了界面组件库，其组件代码采用了面向对象的思想，便于继承及扩展。
当对前端界面联动需求较为复杂的时候，基于dojo的页面组件将是首选，因为其可以将界面中某一个具有共性的区域抽象出来，封装这一区域的界面行为以及数据，可以用搭积木的方式完成复杂页面的开发。
#### 2.3.4	REST介绍
REST（Representational State Transfer）是Roy Field 博士在2000年他的博士论文中提出俩的一种软件架构风格。REST本身并不涉及任何新的技术，它基于HTTP协议，比起SOAP和XML-RPC来说它更加的简洁高效，现在越来越多的大型网站正在使用REST风格来设计和实现[12]。
REST最突出的特点就是用URI来描述互联网上的所有资源，Roy Fielding博士通过观察互联网的运作方式对其进行了抽象，他认为：设计良好的网络应用表现为一系列的虚拟“网页”，或者说这些虚拟网页就是资源状态的表现；用户选择这些链接导致下一个虚拟的“网页”传输到用户端展现给使用的人，而这正代表了资源状态的转发。
REST风格主要有以下的特点：
资源通过URI来指定和操作。
对资源的操作包括获取、创建、修改和删除资源，这些操作正好对应HTTP协议提供的GET、POST、PUT、DELETE、方法。
连接是无状态的。能够利用Cache机制来提高性能。
## 3  系统总体设计
### 3.1	 系统需求分析
如今互联网+已经深入我们的生活，网上订餐的兴起方便了我们的同时也带来了一些问题，订单派送不及时不仅会引起客户的不满，同时客户的投诉也会影响到订单配送员和商家的利益。本系统为提升送餐效率，缩短配送时间，从商家接单系统入手，利用ArcGIS API for JavaScript将订单信息实时显示在地图上，同时规划好配送路径，为配送员提供辅助决策，减少其使用手机搜索地图规划路径的时间，提升效率。
### 3.2  系统框架设计
本系统主要由用户表现层，应用服务层以及数据层构成，其中用户表现层指的是浏览器端，用html、css、JavaScript写成基本网页框架，然后引用ArcGIS API for JavaScript和其他的模块控制脚本完成；应用服务层主要是由ArcGIS Server发布的地图服务构成，包括网络分析服务NAserver、地理编码服务GeocodeServer；数据层，指所用到的数据库，这里采用的是ArcGIS Online提供的在线切片地图占用资源少加载速度快，还有利用ArcMap数据处理过的网络路径数据集。
系统总体框架图如下：
![image](http://dlwxz.com/bs2.png)
图 2 系统总体框架图
### 3.3  系统功能设计
系统分为前端和后端，前端主要是浏览器，后端主要是ArcGIS Server。通过辅助系统与服务器的交互实现所有功能，系统的主要功能有最短路径分析模块，显示等待时间模块，和显示等待距离模块。
系统功能框架图如下：
![image](http://dlwxz.com/bs3.png)
图 3 系统功能框架图
## 4  系统功能开发
### 4.1数据获取与处理
首先从网上下载南昌市青山湖区矢量地图，用ArcMap打开，将其转换为栅格数据，利用矩形方法选择四个点裁剪为所需要的区域大小，然后将其转换为shp文件，再利用空间分析工具中的clip工具，按照刚才所裁剪的范围对矢量数据进行裁剪，得到所需要范围的矢量数据，如下图所示：
![image](http://dlwxz.com/bs4.png)
图 4 东华理工周边矢量图
### 4.2  ArcGIS Server发布网络分析服务
#### 4.2.1利用ArcMap创建地图文档
1）导入事先处理好的区域矢量数据，并在工具中选择所有道路要素生成网络数据集。网络数据集属性如下： 
![image](http://dlwxz.com/bs5.png)
图 5 网络数据集属性
2）单击自定义>扩展模块，然后选中Network Analyst扩展模块以启用，单击自定义>工具条>Network Analyst 以显示NetWork Analyst工具条[13]。
3）单击“标准工具”工具条中的添加数据，向地图中添加创建网络分析图层 
时需要用到的网络数据集。导航到数据库中的ecut_ND网络数据集，系统将会提示参与ecut_ND中的所有要素类都添加到地图中时，单击否。
4）单击 Network Analyst 工具条上的 Network Analyst 下拉菜单并选择新建路径，将路径网络分析图层添加到地图中，如下图所示：
![image](http://dlwxz.com/bs6.png)
图 6 添加网络分析图层
在 ArcMap 中创建网络分析图层时，该图层将自动绑定到活动网络数据集。创建路径分析图层时ecut_ND网络处于活动状态，所以这两个图层绑定在一起。此外，网络分析图层还引用通过文件路径绑定到的网络数据集，所以在 ArcMap中不再需要网络数据集图层。
5）右键单击 Streets_ND 或网络数据集图层的名称，然后单击移除。尽管地图是空白的，但存在路径分析图层并且可以发布为服务。将发布的网络分析服务最终添加到地图时，最初不会显示任何要素，直到生成分析结果。要在使用网络分析服务时提供附加背景信息，也可使用缓存的地图服务，例如 ArcGIS Online提供的服务。
6）单击文件>另存为保存地图文档。
 4.2.2发布网络分析服务
1）在ArcMap中，从主菜单中选择文件>共享为>服务，在共享服务窗口中，选择发布服务，单击下一步，在发布服务对话框中，单击连接到ArcGIS Server以创建一个到服务器的新连接，如下图所示： 
![image](http://dlwxz.com/bs7.png)
图 7 连接ArcGIS Server服务器发布服务
2）在添加ArcGIS Server窗口中，选择发布GIS服务。单击下一步。对于服务器URL输入要连接的站点URL：http://localhost:6080/arcgis/admin，服务器类型为ArcGIS Server。
3）发布过程中，将创建服务定义文件并将其临时存储在本地磁盘上。发布过程完成后，服务定义将上传到服务器并删除本地文件。如果服务器管理员已经为站点启用了安全功能，则输入用户名和密码。单击完成。也可以在发布服务窗口中，输入新的服务名称。名称长度不能超过120个字符，并且只能包括字母数字字符和下划线。默认情况下，服务会发布到ArcGIS Server 的根（root）文件夹中。也可以将服务组织到根文件夹下的子文件夹中。选择要将服务发布到其中的文件夹，或创建一个包含服务的新文件夹。单击继续。
4）服务编辑器随即显示，使用服务器可选择对网络分析服务执行的操作。单击功能选项卡，默认情况下地图和KML两项功能自动启用。选中网络分析，服务编辑器如下图所示： 
![image](http://dlwxz.com/bs8.png)
图 8 服务编辑器
5）在服务编辑器的左侧窗格中，单击网络分析选项卡，然后查看对网络分析服务启用的操作和属性。本系统以默认值继续。
6）单击分析，该操作可用以对地图文档进行检查，看其是否能够发布到服务器。在准备窗口中修复任何错误；该操作必须在发布之前完成，另外还可以修复警告信息和通知消息，以进一步完善网络分析服务的性能和外观。修复错误后，单击发布
7）打开浏览器登录ArcGIS Server Manager查看服务，如下图所示：
![image](http://dlwxz.com/bs9.png)
图 9 ArcGIS Server管理器
8） 发现服务正在停止，这是经常遇到的一个问题，解决方法重新配置一下ArcGIS Server 账户即可，查看服务功能如下图所示：
![image](http://dlwxz.com/bs10.png)
图 10 ecut_xg服务的功能
### 4.3网页基础框架构建
1）建立文档，屏幕主要分为两个部分，左侧为信息展示，右侧为地图与路径展示，书写html，相关html代码如下：

     <!DOCTYPE html>
     <html>
     <head>
     <meta charset="utf-8">
     <meta name="viewport"，content="initial-scale=1,maximum-scale=1,user-scalable=no">
     <title>外卖辅助决策系统</title>
     <style> </style>
     <body id="body">
    <div id="left">
     <div id="nav">
      <div id="wtime">等待时间</div>
      <div id="distance">距离/m</div>
     </div>
     <ul id="uli">
     </ul>
     </div>
     <div id="viewDiv"></div>
     </body>
 
     </body>
     </html>
2）引入ArcGIS api for JavaScript 的脚本以及样式，本系统使用的是最新版本4.6的在线资源，如下：

    <link rel="stylesheet" href="https://js.arcgis.com/4.6/esri/css/main.css">
    <script src="https://js.arcgis.com/4.6/"></script>
3）装载模块，通过requre语句引用API中的相关资源

       require( [
      "esri / Map",
      "esri / views / MapView",
      "dojo / dom" ,
      "dojo / domReady!"
         ] ,  function(
      Map, MapView, 
      ) {}
4）创建地图和视图，底图为ArcGIS 在线地图，以东华理工大学为中心点，缩放层级为16；

    var map = new Map({
        basemap : "osm",
      });
      var view = new MapView({
        container : "viewDiv", //
        map : map, 
        center : [115.825, 28.718],
        zoom : 16
      });
       5）设置相关css样式，如下所示
        html,body {
      padding : 0;
      margin : 0;
    }
    #viewDiv {
      padding : 0;
      margin: 0;
    height: 100%;
      width: 80%;
      position: absolute;
      right: 0;
    }
    #left {
      padding: 0;
      margin: 0;
      width: 20%;
      height: 100%;
      position: absolute;
      left:0;
      background-color: rgba(25, 25, 25, 0.2);
    }
    #nav {
       padding: 0;
       margin: 0;
       width: 100%;
    }
    #nav div{
      padding: 0;
      margin: 1px;
      background-color: rgba(200,200,0,0.5);
      width: 49%;
      height: 100%;
      float: left;
      border-radius:5px;
      text-align:center;
    }
    #uli li li{
      padding: 0;
      margin: 1px;
      background-color: rgba(0,200,0,0.5);
      width: 49%;
      height: 100%;
      float: left;
      border-radius:1px;
      text-align:center;
      list-style:none;
    }
    #uli {
      clear: both;
      padding: 0;
      margin: 0;
      width: 100%
      background-color:red;
      list-style:none;
    }
基本页面展现如图11所示：
![image](http://dlwxz.com/bs11.png)
图 11 基本页面效果展示
### 4.4最短路径模块
1）首先请求ArcGIS API for JavaScript包中的资源

    require([
    "esri / Map",
    "esri / views / MapView",
    "esri / Graphic",
    "esri  / layers / GraphicsLayer",
    "esri  / tasks / RouteTask",
    "esri  / tasks / support / RouteParameters",
    "esri  / tasks / support / FeatureSet",
    "esri  / core / urlUtils",
    "dojo / on",
     "dojo / domReady!"
    ], function(
      Map, MapView, Graphic, GraphicsLayer, RouteTask, RouteParameters,
      FeatureSet, urlUtils, on
)
2）设置代理页处理路线服务和通信，然后将URL指向本地服务器的Route服务，在ArcGIS Server管理器中引用，如下图所示：
![image](http://dlwxz.com/bs12.png)
图 12 网络分析服务的 REST API

    urlUtils.addProxyRule({
    urlPrefix : " route.arcgis.com " ,
    proxyUrl : "/ sproxy / "
      } ) ;   // 将URL指向有效的路由服务
    var routeTask = new RouteTask({
     url:"http://localhost:6080/arcgis/rest/services/ecut/ecut_xg/NAServer/Route"
    });
3）定义routeLyr图层，利用Graphicslayer()创建，客户的位置点和计算的路径结果存储在这个图层中

     var routeLyr = new GraphicsLayer();
4）定义routeParams，创建RouteParametes()的实例，定义stops点的数组，用FeatureSet()创建；空间参考采用的是WGS84，对应的wkid值为4326

     var routeParams = new RouteParameters ( {
        stops: new FeatureSet ( ) ,
        outSpatialReference : { // 自动添加新的空间参考
        wkid : 4326
        }
      });
5）定义符号系统，客户点是stopSymbol，商家点为markerSymbol，路径为routeSymbol。可以设置其中的type，color，和width。系统系统

      // 定义用于显示停靠点的符号系统
      var stopSymbol = {
        type : " simple-marker " , // 自动添加新的SimpleMarkerSymbol（）
        style : " cross ",
        size : 15,
        outline: { // 自动添加新的SimpleLineSymbol( )
          width : 4
        }
      } ;
      var markerSymbol  =  {
        type: " simple-marker " , 
        color : [ 226 ,  119 ,  40 ] ,
        outline: { 
          color : [255 ,  0 ,  0] ,
          width : 1
        }
      } ;
      // 定义用于显示路线的符号系统
      var routeSymbol = {
        type : " simple-line " , 
        color : [0,  0 ,  255 ,  0.5 ] ,
        width : 5
      };
6）添加addStop事件并使用on()监听click事件绑定到addStop，首先定义stop图形，符号指向stopSymbol，几何指向事件触发的地图点；然后将stop点集合加入到routeLyr和routeParams.stops.festures当中；然后判断stops点集如果长度大于2，也就是说定义了第一个客户点之后，routeTask就解析routeParams然后执行showRoute回调函数展示路径；因为这是一个一对多的路径展示，最后还要splice掉这个客户点，这样下次点击时routeParams中还是有两个点，这样就绘制了同一起点到不同终点的路径。

     on( view ,  " click " ,  addStop) ;
     function addStop ( event )  {
        var stop = new Graphic ( {
          geometry : event.mapPoint,
          symbol : stopSymbol
        } ) ;
        routeLyr.add ( stop ) ;
        routeParams.stops.features.push ( stop ) ;
        if ( routeParams.stops.features.length  >=  2 )  {
          routeTask.solve( routeParams ) . then ( showRoute ) ;
          routeParams.stops.features.splice ( 1 , 1 ) ;
        }  
      }
7）定义showRoute函数，参数为data，首先定义一个routeResult路线结果变量，将其指向data的route，它实际上是一个graphic，然后将它的符号指向路径符号routeSymbol，最后将这个routeResult路线结果变量添加到routeLyr路线图层当中去。

    function showRoute ( data )  {
        var routeResult = data.routeResults[0].route ;
        routeResult.symbol  = routeSymbol ;
        routeLyr.add( routeResult ) ;
      }[7]
最短路径结果预览如图13所示：（图中红色圆表示起点）
![image](http://dlwxz.com/bs13.png)
图 13 路经分析结果图
### 4.5  等待时间模块
本模块实际上就是利用JavaScript当中的计时器原理，通过每次点击屏幕触发setInterval()，再添加到列表当中。
1）定义showInfo()函数，设置li，首先获取ul列表的总ID定义为oUl变量，然后创建元素li，定义为oLi，再创建Li1元素，并利用appendChild将其追加到oLi节点下面。并且要做一个判断，如果oUl没有子节点就直接追加，有子节点则在子节点的前面插入
2）定义变量n，做为时间显示变量，初始化为0
3）定义计时器setInterval(function() , 1000);1秒触发一次，60秒后显示为分
4）代码如下：

      function showInfo( data )[14]  {
         oUl = document.getElementById( ' uli ' ) ;
         oLi = document.createElement ( ' li ' ) ;
         oLi.id = " lis " ;
         var li1 = document.createElement( ' li ' ) ;
         oLi.appendChild( li1 ) ;
         var n = 0 ;
         t = setInterval( function ( ) {
          n++ ;
          li1.innerHTML = n + " 秒 " ;
          if ( n >= 60 ) {
            li1.innerHTML = Math.floor( n/60 ) + " 分 " ;
          }
         } , 1000 )
         if ( oUl.children [ 0]  ) {
          oUl.insertBefore( oLi, oUl.children [ 0 ] ) ;
        } else {
          oUl.appendChild ( oLi ) [15];
        }
      }
效果预览如图14所示：（红色圆表示起点）
![image](http://dlwxz.com/bs14.png)
 图 14 等待时间效果图
### 4.6  显示距离模块
1）本模块计算距离是利用坐标计算的两点之间的距离，因此要用到地理编码服务，首先在之前的基础上请求ArcGIS API for JavaScript资源，引入Locator

    Require ([
    “ esri / tasks / lacator ” ,
    ]) , function ( Locator)
2）定义LocatorTask，通过Locator引入url，url可登录ArcGIS Server，在services目录下的World中

       Var  locatorTask  =  new  Locator ( {
        url : " https://geocode.arcgis.com/arcgis/rest/services/World/GeocodeServer        "
      } ) ;
3）定义m和i两个全局变量做为参数用，定义latArray和lonArray两个数组，用于存放点的经纬度

     var m = - 1 ;
     var latArray = new Array ( ) ;
     var lonArray = new Array ( ) ;
     var I = - 1 ;
4）在addStop函数中定义lat和long变量，分别记录点的纬度和经度，设置好第一个点的符号做为起始商家点

        lat = Math.round( event.mapPoint.latitude  *  1000 )  /  1000 ;
        lon = Math.round( event.mapPoint.longitude  *  1000 )  /  1000 ;
        //将第一个点作为商家点
        m++ ;
        if ( m == 0 )
        {
          stop.symbol = markerSymbol ;
        }
5）根据stops的长度，小于2时，也就是将起始商家点的坐标加进数组，大于2时将每一个点的坐标加进数组，实际上每个数组中只有第1个点和第m个点的信息，通过点击地图，m增加，执行showInfo时同时执行showLength；

          if (routeParams.stops.features.length  <  2 ) {
          latArray.push( lat ) ;
          lonArray.push( lon ) ;  
        }
        // 将其他点的坐标加进数组
        if ( routeParams.stops.features.length  >=  2 )  {
          routeTask.solve(routeParams).then( showRoute ) ; 
          latArray.push( lat ) ;
          lonArray.push( lon ) ; 
          // alert(latArray[ m ] ) ;
          showInfo ( ) ;
          routeParams.stops.features.splice ( 1 , 1) ;
        }
6）定义showLength函数，将第m个点与第1个点做运算，计算出纬度差和经度差，转换单位和距离，利用三角函数求解，用return返回计算结果
      
        function showLength( ){
        var R = 6378137;  //地球半径（米）  
        var lat1 = latArray[ 0 ]  *  Math.PI  /  180.0 ;  
        var lat2 = latArray[ m ]  *  Math.PI  /  180.0 ;
        var lon1 = lonArray[ 0 ]  *  Math.PI  /  180.0 ;
        var lon2 = lonArray[ m ]  *  Math.PI  /  180.0 ; 
        var a = lat1 - lat2 ;
        var b = lon1 - lon2 ; 
        var sa2 = Math.sin ( a  /  2.0 ) ;  
        var sb2 = Math.sin ( b  /  2.0 ) ;  
        var dis = 2 * R * Math.asin ( Math.sqrt (sa2 * sa2 + Math.cos ( sa2 ) * Math.cos ( lat2 ) * sb2 * sb2 ) ) ;  
        var d = Math.ceil( dis ) ;
        return d ;
       }
7）在showInfo中添加li2子节点，调用showLength

         var li2 = document.createElement( ' li ' ) ;
         oLi.appendChild( li2 ) ;
         var r = showLength ( );
         li2.innerHTML =  r ;

效果预览如图15所示：（红色圆表示起点）
![image](http://dlwxz.com/bs15.gif)
图 15 显示距离效果图 
## 5  结论与展望
### 5.1结论
本系统充分利用ArcGIS API for JavaScript和前端技术，将外卖接单平台以图的方式将其呈现出来，具有一定的可视性和实时性。然而如果在实际中应用的话，其中仍然存在着不少问题，主要是系统结构功能不够完善。首先当订单数量较多时，图上的路径就会变得多而繁杂，使可视化效果下降，而且不同的订单，本人并没有设计编号或者颜色进行区分，无法区分哪条订单与哪条路径对应；其次本人发布的网络分析路径图层有些不够准确，主要是在ArcMap中处理矢量图时没有进行拓扑分析造成的，使得有些路径不够准确；再者距离和等待时间显示存在延迟，这是函数嵌套调用时造成的；然后，对于已经配送和没有配送的订单没有进行设计以进行区分；最后由于本人掌握知识有限，没有能够将多点路径进行整合，也没有设计算法将时间和距离的权重进行对比完成综合的成本路径分析。总而言之，本系统只是提供一个大概思路的模板，以后还需要完善内部代码的逻辑以及增加后台数据库设计等，将WebGIS充分应用到人们生活中去。
### 5.2展望
外卖员做为一个危险职业，目前仍然无法取代，然而第四次工业革命的崛起，人工智能的发展，无人驾驶技术的进步，使得让无人机或者机器人代替人类送外卖成为了可能。像亚马逊、京东均已经具备了无人机送货的能力，可是仍受到相关条件的限制，比如电池的续航能力，机器的传感器及成本控制，不可控的天气变化，城市密集区的安全问题，相关的法律法规限制，以及机器的中枢控制系统等等。相信在不久的将来这些问题都会被人类解决，机器一定可以准确安全地自主规划路径完成配送，而webgis 必将在其中大显身手，基于webgis的路径导航决策系统将与控制系统融为一体，先进的人工智能必将帮助人们更好地建造智慧城市，打造智慧生活。



