$(function () {
	var categories = [
			'Land Features',
			'Bay','Channel','Cove','Dam','Delta','Gulf','Lagoon','Lake','Ocean','Reef','Reservoir','Sea','Sound','Strait','Waterfall','Wharf', // Water Features
			'Amusement Park', 'Historical Monument', 'Landmark', 'Tourist Attraction', 'Zoo', // POI/Arts and Entertainment
			'College', // POI/Education
			'Beach', 'Campground', 'Golf Course', 'Harbor', 'Nature Reserve', 'Other Parks and Outdoors', 'Park', 'Racetrack',
				'Scenic Overlook', 'Ski Resort', 'Sports Center', 'Sports Field', 'Wildlife Reserve', // POI/Parks and Outdoors
			'Airport', 'Ferry', 'Marina', 'Pier', 'Port', 'Resort', // POI/Travel
			'Postal', 'Populated Place'
		],
		cats = categories.join(','),
		overrides = {
			'08736, Manasquan, New Jersey, USA': {x: -74.037, y: 40.1128},
			'32899, Orlando, Florida, USA': {x: -80.6774, y: 28.6143},
			'97003, Beaverton, Oregon, USA': {x: -122.8752489, y: 45.5050916},
            '99734, Prudhoe Bay, Alaska, USA': {x:-148.3372, y: 70.2552},
            
            'Guam, Oceania': {x: 144.74, y: 13.46},
			'Andover, Maine, United States': {x: -70.7525, y: 44.634167},
			'Bear Creek, Pennsylvania, United States': {x: -75.772809, y: 41.204074},
			'Bear Creek Village, Pennsylvania, United States': {x: -75.772809, y: 41.204074},
			'New York City, New York, United States': {x: -74.0059, y: 40.7142},
			'Pinnacles National Monument, San Benito County,California, United States': {x: -121.147278, y: 36.47075},
			'Pinnacles National Park, CA-146, Paicines, California': {x: -121.147278, y: 36.47075},
			'Welcome, Maryland, United States': {x: -77.081212, y: 38.4692469}
		},
		roundToPlaces = function (num, decimals) {
			var n = Math.pow(10, decimals);
			return Math.round( (n * num).toFixed(decimals) )  / n;
		},
		doRedirectToGeometry = function (geom) {
			var location = window.location,
				query    = '?lat=' + roundToPlaces(geom.y,4) + '&lon=' + roundToPlaces(geom.x,4),
				origin, domain;
			if (location.pathname.match(/MapClick.php$/)) {
				if (location.origin) {
					origin = location.origin;
				} else {
					origin = location.protocol + "//" + location.hostname + (location.port ? ':' + location.port: '');
				}
				window.location = origin + location.pathname + query;
			} else {
				if (location.hostname.match(/dev\.nids\.noaa\.gov$/)) {
					domain = 'forecast.dev.nids.noaa.gov';
				} else if (location.hostname.match(/preview.*\.weather\.gov$/)) {
					domain = 'preview-forecast.weather.gov';
				} else {
					domain = 'forecast.weather.gov';
				}
				window.location = location.protocol + '//' + domain + '/MapClick.php' + query;
			}
		};

	$('#getForecast #inputstring').devbridgeAutocomplete({
		serviceUrl: '//geocode.arcgis.com/arcgis/rest/services/World/GeocodeServer/suggest',
		deferRequestBy: 300,
		paramName: 'text',
		params: {
			f: 'json',
			countryCode: 'USA,PRI,VIR,GUM,ASM',
			category: cats,
			maxSuggestions: 10
		},
		dataType: 'jsonp',
		transformResult: function (response) {
			return {
				suggestions: $.map(response.suggestions, function (i) {
					return {
						value: i.text,
						data: i.magicKey
					};
				})
			};
		},
		minChars: 3,
		showNoSuggestionNotice: true,
		noSuggestionNotice: 'No results found. Please try a different search string.',
		onSelect: function (suggestion) {
			var request;
			if (overrides[suggestion.value]) {
				doRedirectToGeometry(overrides[suggestion.value]);
			} else {
				request = $.ajax({
					url: '//geocode.arcgis.com/arcgis/rest/services/World/GeocodeServer/find',
					data: {
						text: suggestion.value,
						magicKey: suggestion.data,
						f: 'json'
					},
					jsonp: 'callback',
					dataType: 'jsonp'
				});
				request.done(function (data) {
					var loc = data.locations[0];
					if (loc) {
						doRedirectToGeometry(loc.feature.geometry);
					} else {
						alert('An unexpected error occurred. Please try a different search string.');
					}
				});
			}
		},
		width: 400
	});

	var ac = $('#getForecast #inputstring').devbridgeAutocomplete();
	$('#getForecast').submit(function () {
		if (ac.suggestions[0]) {
			$(ac.suggestionsContainer.children[0]).click();
		}
		return false;
	});
});
