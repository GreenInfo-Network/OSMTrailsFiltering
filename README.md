# OSMTrailsFiltering

Documentation for how we download OSM and extract trails, then crop to CPAD, for use as the trails overlay.

# The Steps

* Install the OSM utilities, if you haven't previously:
    sudo apt-get install osmctools osmosis osmjs

* Fetch the latest California extract from Geofabrik
    Use the DBF formatted one, the whole dataset.
    http://download.geofabrik.de/north-america/us/california.html

* Convert the PBF and extract the desired features:
    osmconvert california-latest.osm.pbf -o=california.osm
    osmfilter california.osm --keep="type=way highway=path highway=footpath route=hiking route=foot" > california-trails.osm

    rm -r california-trails-shapefiles
    ogr2ogr -f 'ESRI Shapefile' -lco SHPT=ARC -skipfailures -overwrite california-trails-shapefiles california-trails.osm

    Now you'll have lines.shp which are trails.
    There's also multilinestrings.shp but it's about 2% of the trails and isn't worth using.

* Reproject CPAD Holdings into EPSG:4326 to match the OSM trails.
    QGIS gives wonky results trying to do a Clip operation and also reprojecting.

    ogr2ogr -t_srs epsg:4326 cpad.shp CPAD_2016a_Holdings.shp

    Tip: Use CPAD Holdings. SuperUnits means fewer polygons, but much larger areas where spatial indexes don't work well.
    There are more Holdings but the spatial intersection is much more efficient.

* Load the trails shapefile, and the current CPAD into QGIS.

* Perform the clip, croppings trails to exist only within CPAD areas:
    Vector / Geoprocessing / Clip
    Input: Lines
    Clip: CPAD
    This exports it to a shapefile, which you can review against CPAD in QGIS and then pass off to Mapbox.
