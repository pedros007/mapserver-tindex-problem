This repo exists to provide sample data demonstrating a potentially confusing aspect of [Mapserver tile index](http://mapserver.org/optimization/tileindex.html).  See [Unexpected result from overlapping images in tile	index](https://lists.osgeo.org/pipermail/mapserver-users/2017-January/079694.html) for the discussion.

---

I want to render the following images using [this tile index](http://bl.ocks.org/d/968e0f11f1fd9b02444c3765bbb285a1) ([geojson](http://bl.ocks.org/d/968e0f11f1fd9b02444c3765bbb285a1/map.geojson)) via the `mapfile.map` with a layer: 

```
LAYER
        NAME my_layer
        TYPE RASTER
        TILEINDEX tindex.shp
        TILEITEM "location"
        OFFSITE 0 0 0
        STATUS OFF
    END
END
```

1. black.tif with pixels having the value `1`

![black.tif](https://github.com/pedros007/mapserver-tindex-problem/blob/master/black.png?raw=true)

2. gray.tif with pixels having the value `128`

![gray.tif](https://github.com/pedros007/mapserver-tindex-problem/blob/master/gray.png?raw=true)

I expect the output of [this WMS request](http://localhost/mapserv?SERVICE=WMS&VERSION=1.1.1&REQUEST=GetMap&FORMAT=image%2Fpng&TRANSPARENT=true&LAYERS=current_solution_raster%2Ccurrent_solution_raster_overview_9%2Ccurrent_solution_raster_overview_6&project_id=%3D1&api_key=64pZXx56MnPTGzV8J5Ud5w&WIDTH=256&HEIGHT=256&SRS=EPSG%3A4326&STYLES=&BBOX=-89.2968750,40.6933594,-89.2089844,40.7812500) to look like this:

![expected.png](https://github.com/pedros007/mapserver-tindex-problem/blob/master/expected.png?raw=true)

However, I actually get the output of gray.tif via the WMS request:

    http://localhost:8080/?SERVICE=WMS&VERSION=1.1.1&REQUEST=GetMap&FORMAT=image%2Fpng&TRANSPARENT=true&LAYERS=my_layer&WIDTH=256&HEIGHT=256&SRS=EPSG%3A4326&STYLES=&BBOX=-89.2968750,40.6933594,-89.2089844,40.7812500

![gray.tif](https://github.com/pedros007/mapserver-tindex-problem/blob/master/gray.png?raw=true)

Should Mapserver be able to handle this?  I have a couple of work-arounds that I'm not crazy about:

1.  Add [Layer masks](http://www.mapserver.org/development/rfc/ms-rfc-79.html) to two separate layers, make two WMS requests and stack the results client side.  This works for two layers.  However, I actually have many images which I need to stack.
2. I could burn in NODATA values (or use a mask) to gray.tif, but I'd rather not modify my source data.

