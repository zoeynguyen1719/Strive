function load_openlayers_map(lat, lon, zone, grid_poly) {
    if (zone) {
        var centroid_lat = parseFloat(lat);
        var centroid_lon = parseFloat(lon);
        var kml_url = "./kml/" + zone + ".kml";
        var zoom = 9;
    } else {
        var area_obj = JSON.parse(grid_poly);
        var centroid_lat = parseFloat(area_obj.centroid_lat);
        var centroid_lon = parseFloat(area_obj.centroid_lon);
        var lonlat1 = ol.proj.fromLonLat([parseFloat(area_obj.lon1),parseFloat(area_obj.lat1)]);
        var lonlat2 = ol.proj.fromLonLat([parseFloat(area_obj.lon2),parseFloat(area_obj.lat2)]);
        var lonlat3 = ol.proj.fromLonLat([parseFloat(area_obj.lon3),parseFloat(area_obj.lat3)]);
        var lonlat4 = ol.proj.fromLonLat([parseFloat(area_obj.lon4),parseFloat(area_obj.lat4)]);
        var zoom = 10;
    }

    var styles = {
      'Polygon': [new ol.style.Style({
        stroke: new ol.style.Stroke({
          color: 'rgba(29, 130, 29, 1)',
          width: 2
        }),
        fill: new ol.style.Fill({
          color: 'rgba(0, 255, 0, 0.45)'
        })
      })]
    };

    var styleFunction = function(feature, resolution) {
      return styles[feature.getGeometry().getType()];
    };

    // Attribution
    var attribution = new ol.Attribution({
        html: 'Tiles &copy; <a href="http://www.esri.com/">ESRI</a>'
    });

    var sourceBasePrefix = '//server.arcgisonline.com/ArcGIS/rest/services/';
    var sourceBaseSuffix = '/MapServer/tile/{z}/{y}/{x}?appid=45ad401c-fa23-4a10-8b2f-a7ad29a3e2a0';

    // Background Layers
    var backgroundLayer  = new ol.layer.Tile();

    var noneSource = null;

    var topoSource = new ol.source.XYZ({
        attributions: [attribution],
        url: sourceBasePrefix + 'World_Topo_Map' + sourceBaseSuffix
    })
    backgroundLayer.setSource(topoSource);

    var streetsSource = new ol.source.XYZ({
        attributions: [attribution],
        url: sourceBasePrefix + 'World_Street_Map' + sourceBaseSuffix
    })

    var satelliteSource = new ol.source.XYZ({
        attributions: [attribution],
        url: sourceBasePrefix + 'World_Imagery' + sourceBaseSuffix
    })

    var oceanSource = new ol.source.XYZ({
        attributions: [attribution],
        url: sourceBasePrefix + 'Ocean/World_Ocean_Base' + sourceBaseSuffix
    })

    // Data Layers
    var dataLayer = new ol.layer.Vector({
        style: styleFunction
    });

    if (zone) {
        var kmlSource = new ol.source.Vector({
            url: kml_url,
            format: new ol.format.KML()
        });
        dataLayer.setSource(kmlSource);
    } else {
         var geojsonGrid = {
          'type': 'FeatureCollection',
          'crs': {
            'type': 'name',
            'properties': {
              'name': 'EPSG:3857'
            }
          },
          'features': [
            {
              'type': 'Feature',
              'geometry': {
                    'type': 'Polygon',
                    'coordinates': [[[ lonlat1[0], lonlat1[1]], [lonlat2[0], lonlat2[1]], [lonlat3[0], lonlat3[1]], [lonlat4[0], lonlat4[1]]]]
              }
            }
          ]
        };

        var jsonSource = new ol.source.Vector({
            features: (new ol.format.GeoJSON()).readFeatures(geojsonGrid),
        });

        dataLayer.setSource(jsonSource);
    }

    var mapView = new ol.View({
        center: ol.proj.fromLonLat([centroid_lon, centroid_lat]),
        zoom: zoom
    });

   var map = new ol.Map({
      layers: [
        backgroundLayer,
        dataLayer
      ] ,
      view: mapView,
      target: 'emap'
    });

    map.on('singleclick', function(evt) {
        var lonlat = ol.proj.toLonLat([evt.coordinate[0],evt.coordinate[1]]);
        var redirect_lon = lonlat[0];
        var redirect_lat = lonlat[1];
        window.location.href = "./MapClick.php?lon=" + redirect_lon + "&lat=" + redirect_lat;
    });

    $('#basemap-selected').change(function() {
        var newSource = eval($(this).find(':selected').val() + 'Source');
        backgroundLayer.setSource(newSource);
    });
};

