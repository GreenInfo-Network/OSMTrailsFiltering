# OSMTrailsFiltering

Documentation for how we download OSM and extract trails, then crop to CPAD, for use as the trails overlay.

# The Steps

* Install the OSM utilities, if you haven't previously:
    sudo apt-get install osmctools osmosis

* Fetch the latest California extract from Geofabrik
    Use the DBF formatted one, the whole dataset.
    http://download.geofabrik.de/north-america/us/california.html

* Convert the PBF and extract the desired features:
    osmconvert california-latest.osm.pbf -o=california.osm
    osmfilter california.osm --keep="highway=path highway=footpath route=hiking route=foot" > california-trails.osm

* Load the resulting OSM into QGIS:
    Tip: Vector -> Openstreetmap -> Import topology from an XML file

* Load the current CPAD content into QGIS.
    Tip: SuperUnits means fewer polygons, so is more efficient.

* Perform the clip, croppings trails to exist only within CPAD areas:
