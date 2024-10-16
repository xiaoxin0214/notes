### GeoServer安装
[安装包下载地址](https://pan.baidu.com/s/1J-v0V0tJ3IEa5uAwHtvzQg?pwd=8jap)

1. `Java`环境安装，`geoserver2.25` 需要依赖`java11`或`java17`环境

   如果已经配置过`java11`或者`java17`可跳过此步骤，后续安装`geoserver`时会自动读取`JAVA_HOME`变量。

   如果没有安装过则解压`jdk-11.0.24_windows-x64_bin.zip`并配置`JAVA_HOME`等环境变量。

   如果已经配置过`java8`环境则直接解压`jdk-11.0.24_windows-x64_bin.zip`后续安装`geoserver` 手动设置

   `java11`目录即可。

2. `geoserver`安装

   双击`GeoServer-2.25.3-winsetup.exe`

   选择`java11`环境目录

   ![](./images/java11环境设置.png)

   设置用户名密码

   ![](./images/设置用户名密码.png)

   设置端口号

   ![](./images/设置端口号.png)

   安装完成后访问[geoserver首页](http://localhost:8080/geoserver)

   ![](./images/geoserver首页.png)

### 瓦片服务发布

1. 输入安装时设置的用户名密码登录`geoserver`

2. 创建工作空间

   ![](./images/创建工作空间.png)

3. 创建存储仓库

   创建存储仓库

   ![](./images/创建存储仓库.png)

   新建数据源，此处使用`PostGIS`

   ![](./images/新建数据源.png)

   输入数据源连接信息

   ![](./images/数据源连接信息.png)

4. 发布图层

   ![](./images/发布图层.png)

   选择要发布的数据

   ![](./images/选择发布数据.png)

   设置范围信息

   ![](./images/设置范围信息.png)

5. 预览wms服务

   ![](./images/预览wms服务.png)

   ![](./images/wms服务预览结果.png)

### 矢量瓦片发布

1. 解压`geoserver-2.25-vectortiles-plugin.zip`到`C:\Program Files\GeoServer\webapps\geoserver\WEB-INF\lib`目录
2. 以管理员权限运行`C:\Program Files\GeoServer\bin`目录下的`stopService.bat`和`startService.bat`脚本重启`geoserver`，发布服务界面图形格式出现如下结果证明插件安装成功,勾选`application/vnd.mapbox-vector-tile`选项即可发布矢量瓦片服务

![](./images/矢量瓦片插件安装结果.png)

3. 矢量瓦片预览

![](./images/矢量瓦片预览.png)

![](./images/矢量瓦片预览结果.png)

### MapBox加载矢量瓦片

1. `geoserver`配置跨域访问

   拷贝`jetty-http-9.4.52.v20230823.jar`,`jetty-servlets-9.4.52.v20230823.jar`,`jetty-util-9.4.52.v20230823.jar`到`C:\Program Files\GeoServer\webapps\geoserver\WEB-INF\lib`目录

   修改`C:/Program Files/GeoServer/webapps/geoserver/WEB-INF/web.xml`配置文件,取消下图两处注释

   ![](./images/geoserver跨域配置.png)

   重启`geoserver`

2. 前端加载

   加载矢量瓦片样式文件可以`maputnik`进行配置,具体操作参考[maputnik配置geoserver矢量瓦片样式]()

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset='utf-8' />
  <title>加载第三方矢量切片</title>
  <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
  <script src="https://api.mapbox.com/mapbox-gl-js/v2.1.0/mapbox-gl.js"></script>
  <link href="https://api.mapbox.com/mapbox-gl-js/v2.1.0/mapbox-gl.css" rel="stylesheet" />

  <style>
    body {
      margin: 0;
      padding: 0;
    }

    #map {
      position: absolute;
      top: 0;
      bottom: 0;
      width: 100%;
    }
  </style>
</head>

<body>
  <div id='map'></div>
  <script>
    mapboxgl.accessToken = 'your accessToken';
    var mapStyle = {
  "version": 8,
  "name": "Empty Style",
  "metadata": {"maputnik:renderer": "mlgljs"},
  "sources": {
    "xinxiao": {
      "type": "vector",
      "tiles": [
          // 参照geoserver矢量瓦片预览请求地址填写
        "http://localhost:8080/geoserver/xinxiao/gwc/service/wmts?REQUEST=GetTile&SERVICE=WMTS&VERSION=1.0.0&LAYER=xinxiao:110100&STYLE=&TILEMATRIX=EPSG:900913:{z}&TILEMATRIXSET=EPSG:900913&FORMAT=application/vnd.mapbox-vector-tile&TILECOL={x}&TILEROW={y}"
      ],
      "scheme": "xyz",
      "minzoom": 0,
      "maxzoom": 24
    }
  },
  "sprite": "",
  "glyphs": "https://orangemug.github.io/font-glyphs/glyphs/{fontstack}/{range}.pbf",
  "layers": [
    {
      "id": "1",
      "type": "fill",
      "source": "xinxiao",
      "source-layer": "110100",//参照实际数据填写
      "layout": {"visibility": "visible"},
      "paint": {
        "fill-color": "rgba(72, 24, 229, 1)",
        "fill-antialias": true
      }
    }
  ],
  "id": "0f6lhej"
}

    var map = new mapboxgl.Map({
      container: 'map',
      style: mapStyle,
      zoom: 14,
      center: [116.08, 40.54]
    });
  </script>

</body>

</html>
```

![](./images/MapBox加载矢量瓦片.png)