# 1 Introduction to geospatial concepts

## 1.1 Introduction to raster data

![](figures/raster_concept.png)

<br>
<br>
<br>
<br>

![](figures/rmd-01-elevation-map-1.png)

<br>
<br>
<br>
<br>

![](figures/USA_landcover_classification.png)

<br>
<br>
<br>
<br>

![](figures/rmd-01-classified-elevation-map-1.png)

<br>
<br>
<br>
<br>

![](figures/spatial_extent.png)

<br>
<br>
<br>
<br>

![](figures/raster_resolution.png)

<br>
<br>
<br>
<br>

![](figures/RGBSTack_1.jpg)

<br>
<br>
<br>
<br>

![](figures/rmd-01-demonstrate-RGB-Image-1.png)

<br>
<br>
<br>
<br>

![](figures/rmd-01-plot-RGB-now-1.png)

<br>
<br>
<br>
<br>

## 1.2 Introduction to vector data

![](figures/pnt_line_poly.png)

<br>
<br>
<br>
<br>

### Which features are represented by which vector type?
![](figures/rmd-02-unnamed-chunk-2-1.png)

<br>
<br>
<br>
<br>

## 1.3 Coordinate reference systems (CRS)

![](figures/0637aa2541b31f526ad44f7cb2db7b6c.jpg)

<br>
<br>
<br>
<br>

A PROJ4 string includes:    

* proj= the projection of the data
* zone= the zone of the data (this is specific to the Universal Transverse Mercator projection)
* datum= the datum use
* units= the units for the coordinates of the data
* ellps= the ellipsoid (how the earth’s roundness is calculated) for the data

`+proj=utm +zone=18 +datum=WGS84 +units=m +no_defs +ellps=WGS84 +towgs84=0,0,0`




![](figures/Utm-zones-USA.svg)
