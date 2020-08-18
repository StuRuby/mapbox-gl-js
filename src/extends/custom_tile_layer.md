# 如何接入kmapcore

## 一、初始化实例

```
const layer = new mapboxgl.CustomTileLayer(type,options);
```


## 二、传递参数

### 1. type === 'WMTS'

options 必须参数 TileGrid, TileGrid参数参考 `ol/tilegrid/WMTS`.

options 其余参数参考`ol/source/WMTS`.

参考示例：
```
const options = {
        crossOrigin: 'anonymous',
        projection: 'EPSG:4326',
        url: 'https://arcgis.sdi.abudhabi.ae/arcgis/rest/services/Pub/BaseMapAra_DarkGray_GCS/MapServer/WMTS?',
        layer: 'Pub_BaseMapAra_DarkGray_GCS',
        format: 'image/jpeg',
        style: 'default',
        matrixSet: 'default028mm',
        wrapX: true,
        tileGrid: {
            origin: [-400.0, 400.0],
            resolutions: [0.011897305029151402, 0.005948652514575701, 0.0029743262572878505,...],
            matrixIds: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19],
        }
}
```

### 2. type === 'XYZ'

options 必须参数 TileGrid, TileGrid参数参考 `ol/tilegrid/WMTS`.

options 其余参数参考`ol/source/XYZ`.


参考示例：
```
const options = {
        crossOrigin: 'anonymous',
        projection: 'EPSG:4326',
        url: 'https://arcgis.sdi.abudhabi.ae/arcgis/rest/services/Pub/BaseMapAra_DarkGray_GCS/MapServer/WMTS?',
        tileGrid: {
            origin: [-400.0, 400.0],
            resolutions: [0.011897305029151402, 0.005948652514575701, 0.0029743262572878505,...],
            matrixIds: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19],
        }
}
```

### 3. type === 'TILE_IMAGE'

options 参考 `ol/source/TileImage`

参考示例

```
const options = {
        type: 'TILE_IMAGE',
        // 切片url地址
        url: "https://10.18.17.196/cloudapi/service/api/egis/base/v1/wmts?",
        // 切片原点
        origin: [-400, 400],
        // 切片投影坐标系: 'EPSG:4326' 或 'EPSG:3857',
        projection: 'EPSG:4326',
        // 切片分辨率,可以在服务中查看。
        resolutions: [
            1.40625,
            0.703125,
            0.46875,
            0.3515625,
            0.28125,
            0.234375,
            0.20089285714285715,
            0.17578125,
            0.15625,
            0.140625,
            0.1278409090909091,
            0.1171875,
            0.10817307692307693,
            0.10044642857142858,
            0.09375,
            0.087890625,
            0.08272058823529412,
            0.078125,
            0.07401315789473684
        ],
        tileUrlFunction: function (tileCoord, pixelRatio, proj) {
            if (!tileCoord) {
                return "";
            }
            var z = tileCoord[0];
            var x = tileCoord[1];
            var y = tileCoord[2] + 1;
            if (x < 0) {
                x = -x;
            }
            if (y < 0) {
                y = -y;
            }
            return "https://10.18.17.196/cloudapi/service/api/egis/base/v1/wmts?layer=vec&tilematrixset=c&Service=WMTS&Request=GetTile&Version=1.0.0&style=default&Format=tiles&TileMatrix=" +
                z + "&TileCol=" + x + "&TileRow=" + y;
        }
    };
```



## 三、鉴权处理

如果需要鉴权处理，需要在option 中注入鉴权方法 `setTileLoadFunction`

鉴权方法参考 `openlayers`中`source`的`setTileLoadFunction`方法

参考示例


```
const options = {
     setTileLoadFunction: function (tile, src) {
                var xhr = new XMLHttpRequest();
                xhr.responseType = 'blob';
                xhr.open('GET', src);
                xhr.setRequestHeader('Authorization',
                    'Basic NzlhMWRiNDE3YWIwNDExZWIwMmIyNDA5ZmRhZWMzNTQ6MTQyYjMxZGNkNGU5NDhlODgyYmZhMWM0N2I5MDI5ZGY='
                );
                xhr.addEventListener('loadend', function (evt) {
                    var data = this.response;
                    console.log(data);
                    if (data !== undefined) {
                        tile.getImage().src = window.URL.createObjectURL(data);
                    }
                });
                xhr.send();
            }
}
```