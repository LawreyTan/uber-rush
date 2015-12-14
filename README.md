# uber rush sdk

Node.js SDK for the UberRUSH API. Make deliveries without ever leaving your CLI.

## Prerequisites

You'll need a client ID and a client secret from Uber. Additionally, you'll probably want to test in the RUSH sandbox before you start actually make people bike around your city.

 1. Register at https://developer.uber.com
 2. Create a new app
 3. Select the delivery sandbox
 4. some more steps
 5. `npm install @mjk/uber-rush`


When using the sandbox, updated delivery statuses must be simulated by you directly, by calling `delivery.updateStatus(<new-delivery-status>);`. 

You must call each status in order.

## API reference

### rush.Delivery
### rush.Location
### rush.Quote
### rush.Item
### rush.Contact
### rush.Courier

## Getting started 

Before making API calls, you must initialize the SDK with your app's ID and secret.

    var rush = require('uber-rush')
    rush.init({
        client_secret: YOUR_CLIENT_SECRET,
        client_id: YOUR_CLIENT_ID,
        sandbox: true
    });

Then you can start a new delivery by providing a description of the item to be transported, the pickup and the dropoff addresses.

	var delivery = new rush.Delivery({
	    item: {
	        title: 'Chocolate bar',
	        quantity: 1,
	        is_fragile: true
	    },
	    pickup: {
	        contact: {
	            first_name: 'Ryan',
	            last_name: 'Cheney
	        },
	        location: {
	            address: '64 Seabring St',
	            city: 'Brooklyn',
	            state: 'NY',
	            postal_code: '11231',
	            country_code: 'US'
	        }
	    },
	    dropoff: {
	        contact: {
	            first_name: 'Karen',
	            last_name: 'Holmes
	        },
	        location: {
	            address: '80 Willoughby St',
	            city: 'Brooklyn',
	            state: 'NY',
	            postal_code: '11201',
	            country_code: 'US'
	        }
	    }
	});



## Delivery statuses

 * **processing**: The delivery request is being processed.
 * **no_couriers_available**: The delivery request timed out with no couriers available.
 * **en_route_to_pickup**: A courier has been assigned and is en route to the pickup location.
 * **at_pickup**: The courier is at the pickup location.
 * **en_route_to_dropoff**: The courier has picked up the item and is en route to the dropoff location.
 * **at_dropoff**: The courier is at the dropoff location.
 * **completed**: The delivery has been successfully completed.
 * **client_canceled**: This delivery was canceled by merchant request. 
 * **returning**: The courier is returning the delivery to the pickup location.
 * **returned**: The delivery has been successfully returned to the pickup location.
 * **unable_to_deliver**: Catch­all reason if courier is unable to deliver.


## Full example

    var rush = require('uber-rush');

    rush.init({
        client_secret: YOUR_CLIENT_SECRET,
        client_id: YOUR_CLIENT_ID,
        sandbox: true
    }).then(function() {
        var delivery = new rush.Delivery({
            item: {
                title: 'Chocolate bar',
                quantity: 1,
                is_fragile: true
            },
            pickup: {
                contact: {
                },
                location: {
                    address: '64 Seabring St',
                    city: 'Brooklyn',
                    state: 'NY',
                    postal_code: '11231',
                    country_code: 'US'
                }
            },
            dropoff: {
                contact: {
                },
                location: {
                    address: '80 Willoughby St',
                    city: 'Brooklyn',
                    state: 'NY',
                    postal_code: '11201',
                    country_code: 'US'
                }
            }
        });

        delivery.quote()
        .then(function(topQuote) {
            console.log('Got quotes', topQuote);

            delivery.on('status', function(status) {
                console.log('delivery status updated: ' + status);
            });
            delivery.on('location', function(loc) {
                console.log('delivery location updated: ', loc);
            });

            delivery.confirm();
        });
    }); 
