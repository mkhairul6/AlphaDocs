# (uns)Table of Contents
1. <a href="#alpha-entities-overview">Alpha Entities Overview</a>
	* <a href="#entities-index">Entities Index</a>
2. <a href="#basics-of-calling-the-api">Basics of Calling the API</a>
3. <a href="#api">API</a>
	* <a href="#getting-stores">Getting Stores</a>
	* <a href="#getting-the-menu">Getting the Menu</a>
	* <a href="#posting-an-order">Posting an Order</a>
	* <a href="#reading-all-orders">Reading all Orders</a>
	* <a href="#order-statuses">Order Statuses</a>
	* <a href="#accepting-an-order">Accepting an Order</a>
	* <a href="#completing-an-order">Completing an Order</a>
	* <a href="#cancelling-an-order">Cancelling an Order</a>
	* <a href="#register-a-customer">Register a Customer</a>
	* <a href="#update-a-customer">Update a Customer</a>
	* <a href="#delete-a-customer-address">Delete a Customer Address</a>
	* <a href="#get-customer-details">Get Customer Details</a>
	* <a href="#login-customer">Login Customer</a>
	* <a href="#logout-customer">Logout Customer</a>
	* <a href="#request-for-customer-password-change">Request for Customer Password Change</a>
	* <a href="#change-customer-password-after-request">Change Customer Password (after Request)</a>
	* <a href="#get-eta">Get ETA</a>
	* <a href="#get-gps-coordinates-from-address">Get GPS Coordinates from Address</a>
	* <a href="#get-address-from-gps-coordinates">Get Address from GPS Coordinates</a>
	* <a href="#get-closest-store-from-address">Get Closest Store from Address</a>
	* <a href="#get-favourite-product-list-for-customer">Get Favourite Product List for Customer</a>
	* <a href="#add-a-favourite-product-for-customer">Add a Favourite Product for Customer</a>
	* <a href="#remove-a-favourite-product-for-customer">Remove a Favourite Product for Customer</a>
	* <a href="#getting-all-promotions">Getting all Promotions</a>
	* <a href="#testing-push-notifications">Testing Push Notifications</a>
4. <a href="#collision-8-extensions">Collision 8 Extensions</a>
	* <a href="#get-all-customers">Get All Customers</a>
	* <a href="#posting-an-order-1">Posting an Order</a>
5. <a href="#masterdemo-extensions">MasterDemo Extensions</a>
	* <a href="#get-app-translation-dictionary">Get App Translation Dictionary</a>
	* <a href="#appending-items-payments-and-promotions-to-an-existing-order">Appending Items, Payments, and Promotions to an Existing Order</a>
	* <a href="#generating-an-qr-code-with-an-order-number">Generating an QR Code with an Order Number</a>
	* <a href="#querying-whether-an-order-is-paid">Querying whether an Order is Paid</a>
6. <a href="#cama-cafe-extensions">Cama Cafe Extensions</a>
	* <a href="#get-app-translation-dictionary-2">Get App Translation Dictionary</a>
	* <a href="#taiwan-addresses">Taiwan Addresses</a>
	* <a href="#get-postal-zones">Get Postal Zones</a>
	* <a href="#get-divisions">Get Divisions</a>
	* <a href="#get-roads">Get Roads</a>
7. <a href="#the-soup-spoon-extensions">The Soup Spoon Extensions</a>
	* <a href="#redeeming-points">Redeeming Points</a>
	* <a href="#resend-souperholic-verification-email">Resend Souperholic Verification Email</a>
	* <a href="#manually-verify-souperholic-email">Manually Verify Souperholic Email</a>
	* <a href="#register-a-card">Register a Card</a>

# Alpha Entities Overview

* **Brand** - Alpha supports multiple Brands. Every other entity is tied to a Brand. 
* **Store** - A place that you make an order to. 
* **Product** - An item that can be sold on its own
* **Category** - A Product can belong to multiple Categories. A Category can have multiple child Categories. 
* **Tag** - A Product can have zero or one or more Tags. A Tag is just a piece of information with an image.
* **Variant** - A Variant is some kind of variation of a Product that the customer must choose one. It has a price. Not all Products have Variants.
* **Modifier Group** - A Product can have multiple Modifier Groups. Each group has a set of Modifiers. This allows a wide range of choices to be presented to the customer
* **Modifier** - Something that modifies the final Product that the customer receives. Can have extra cost. 
* **Availability** - A day + time range for when something is available 

## Entities Index

* <a href="#address">Address</a>
* <a href="#availability">Availability</a>
* <a href="#brand">Brand</a>
* <a href="#banner">Banner</a>
* <a href="#category">Category</a>
* <a href="#customer">Customer</a>
* <a href="#coordinates">Coordinates</a>
* <a href="#discount">Discount</a>
* <a href="#extracharge">ExtraCharge</a>
* <a href="#product">Product</a>
* <a href="#store">Store</a>
* <a href="#table">Table</a>
* <a href="#tag">Tag</a>
* <a href="#modifier-group">Modifier Group</a>
* <a href="#modifier">Modifier</a>
* <a href="#modifieritem">ModifierItem</a>
* <a href="#order">Order</a>
* <a href="#orderitem">OrderItem</a>
* <a href="#payment">Payment</a>
* <a href="#pager">Pager</a>
* <a href="#promotion">Promotion</a>
* <a href="#rewards">Rewards</a>
* <a href="#variant">Variant</a>
* <a href="#voucher">Voucher</a>

# Basics of Calling the API

It will be at something like `/api/<version>/<service>/<brandCode>`

* GET for reading
* POST for modifying something
* Response is always a JSON
* If success, response will be `{ status: true [, code: <optional unique integer> ], property1: etc, property2: etc }`
* If failure, it will be `{ status: false, message: “Hopefully some reason why it failed” [, code: <optional unique integer> ] }`
* All entities will have the property `id`. It is a long value that is only unique for that entity
* Images will have the property `imageId`. The value will be something like “awblbnmbgwpbzpcc8sux” This is an ID to a resource on Cloudinary. It can also be null. The URL for the image will be `http://res.cloudinary.com/savantdegrees/image/upload/c_<crop-type>,h_<height>,w_<width/<id>` E.g <http://res.cloudinary.com/savantdegrees/image/upload/c_fit,h_400,w_300/awblbnmbgwpbzpcc8sux> There are many ways to customize the image, get different dimensions and weird transformations. See <http://cloudinary.com/documentation/image_transformations>
* For customer related APIs, an `authToken` is required (see Login Customer)
* Any customer related requests with an invalid authToken will return `{ success: false, code: -666, message: “Some message you can display to the user” }`. The user should be redirected to the login flow
* If the API Authentication Key is set at a brand, every API request to that brand must pass in that key. The key can be passed in as a request parameter `authorization` or a request header `Authorization`. E.g. <http://alpha.savantdegrees.com/api/1.0/menu/ALT?authorization=jO0fHEBl1C2d0WOGGIJt6amAwI84msle>
* It is recommended to set this key before you go live otherwise anyone can simply send orders to that brand

# API

## Getting Stores

`http://<server>/api/1.0/stores/<brandCode>`

### Response:
<pre>
{
	success: true,
	stores: Array of Stores
}
</pre>

#### Store:
<pre>
{
	id: ID,
	name: string,
	delivery: boolean, whether this store does delivery,
	takeAway: boolean,
	dineIn: boolean,
	address: Address object,
	coord: Coordinates object,
	email: string,
	phone: string,
	table: Array of Table,
	openingHours: Array of Availabilities,
	dineInHours: Array of Availabilities,
	takeAwayHours: Array of Availabilities,
	deliveryHours: Array of Availabilities,
	openingHoursString: string, a nicely formatted human readable text from the 'openingHours' property,
	dineInHoursString: string, a nicely formatted human readable text from the 'dineInHours' property,
	takeAwayHoursString: string, a nicely formatted human readable text from the 'takeAwayHours' property,
	deliveryHoursString: string, a nicely formatted human readable text from the 'deliveryHours' property,
	imageId: string,
	deliveryMaxOrderValue: decimal number or null, apps should not allow a delivery order larger than this value
	deliveryMinOrderValue: decimal number or null, apps should not allow a delivery order smaller than this value
}
</pre>


#### Address:
<pre>
{
	line1: string,
	line2: string,
	city: string,
	countryCode: string, 2 character country code,
	postalCode: string,
	string: string of the address in one line
}
</pre>


#### Coordinates:
<pre>
{
	lat: double, latitude
	lng: double, longitude
}
</pre>

#### Table:
<pre>
{
	id: ID,
	number: string, human readable number
}
</pre>

Sample: <http://alpha.savantdegrees.com/api/1.0/stores/ALT?authorization=jO0fHEBl1C2d0WOGGIJt6amAwI84msle>

## Getting the Menu

`http://<server>/api/1.0/menu/<brandCode>[/<storeId>]`

If `storeId` is passed in, only the available menu for that particular store is returned.

### GET Parameters:
* showAll: boolean, default is false, if true returns all products regardless whether they are available or not

### Response:
<pre>
{
	success: true,
	categories: Array of Categories,
	products: Array of Products,
	tags: Array of Tags,
	brand: Brand object
	banners: Array of Banners,
}
</pre>

#### Brand:
<pre>
{
	id: ID,
	imageId: string,
	name: string,
	apiCode: string,
	defaultImageId: string, default product image the client should use if any product does not have an image, beware - this can be null also
	enableProductImages: boolean
}
</pre>

#### Tag:
<pre> 
{
	id: ID
	imageId: string,
	name: string,
	type: string
}
</pre>

#### Category:
<pre>
{
	id: ID,
	imageId: string,
	image2Id: string,
	image3Id: string,
	availabilities: Array of Availabilities, if empty, this Category is available the whole day,
	name: string,
	description: string,
	sortIndex: integer, can be used for sorting Categories in ascending order,
	children: Array of child Categories
}
</pre>

#### Product:
<pre>
{
	id: ID,
	imageId: string,
	image2Id: string,
	image3Id: string,
	name: string,
	description: string,
	sortIndex: integer, can be used for sorting Products in ascending order,
	price: decimal number,
	dineIn: boolean, if Store ID is passed in, whether this Product is available for dine-in right now. If Store ID is not passed in, whether ANY Stores have this available for dine-in,
	delivery: boolean, if Store ID is passed in, whether this Product is available for delivery right now. If Store ID is not passed in, whether ANY Stores have this available for delivery, 
	takeAway: boolean, if Store ID is passed in, whether this Product is available for take away right now. If Store ID is not passed in, whether ANY Stores has this available for take-away,
	allDineIn: boolean, only appears when Store ID is not passed in, whether ALL Stores have this Product avaiable for dine-in right now,
	allTakeAway: boolean, only appears when Store ID is not passed in, whether ALL Stores have this Product avaiable for take-away right now,
	allDelivery: boolean, only appears when Store ID is not passed in, whether ALL Stores have this Product avaiable for delivery right now,
	categories: Array of Category IDs
	modifierGroups: Array of Modifier Groups,
	tags: Array of Tag IDs,
	variants: Array of Variants
}
</pre>

#### Modifier Group:
<pre>
{
	id: ID,
	name: string,
	description: string,
	sortIndex: integer, can be used for sorting Modifier Groups in ascending order,
	combo: boolean, whether this group contains a combo of other Products,
	minModifiers: integer, minimum number of Modifiers that must be selected for this group,
	maxModifiers: integer, maximum number of Modifiers that can be selected for this group,
	modifiers: Array of Modifiers
}
</pre>

#### Modifier:
<pre>
{
	id: ID,
	name: string,
	description: string,
	sortIndex: integer, can be used for sorting Modifiers in ascending order,
	price: decimal number,
	minQuantity: minimum number of this Modifier that must be selected,
	maxQuantity: maximum number of this Modifier that can be selected,
	product: ID of a Product if this Modifier belongs in a Modifier Group that is a combo, otherwise will be null
	preSelected: boolean, whether this Modifier should already be selected for the customer, of course the customer may end up selecting another Modifier
}
</pre>

#### Availability:
<pre>
{
	dayOfWeek: “MONDAY|TUESDAY|etc..”,
	startTime: string, format “HH:mm:ss.ms”,
	endTime: string, format same as above
}
</pre>

#### Banner:
<pre>
{
	id: ID,
	imageId: string,
	product: ID of product this banner is for, or null, apps should go directly to this product page if it is not null
	sortIndex: integer,
	link: URL string or null,
	name: string,
	startTime: milliseconds since epochTime, time to start showing this banner,
	endTime: milliseconds since epochTime, time to stop showing this banner,
	target: “PRODUCT|LINK|NONE”,
	position: ”TOP|BOTTOM” or null,
	description: string,
	type: "GENERAL|ABOUT_BRAND|ONBOARDING" or null
}
</pre>

#### Variant:
<pre>
{
	id: ID,
	price: decimal number,
	modifierGroups: Array of Modifier Groups,
	name: string
}
</pre>

#### Samples:
1. <http://alpha.savantdegrees.com/api/1.0/menu/ALT?authorization=jO0fHEBl1C2d0WOGGIJt6amAwI84msle>
2. <http://alpha.savantdegrees.com/api/1.0/menu/MLP?authorization=jO0fHEBl1C2d0WOGGIJt6amAwI84msle>
3. <http://alpha.savantdegrees.com/api/1.0/menu/MLP/19?authorization=jO0fHEBl1C2d0WOGGIJt6amAwI84msle>

Note that at store 19, only takeaway is available for that product.

## Posting an Order

`http://<server>/api/1.0/order/<brandCode>`

### POST body:
<pre>
{
	type: “DELIVERY|TAKE_AWAY|DINE_IN|TEST”,
	items: Array of OrderItems,
	payments: Array of Payments,
	discounts: Array of Discounts,
	store: ID of store that is receiving this order,
	table: ID of table for this order, can be NULL,
	authToken: authentication token of current customer (if null or missing, this is a guest order),
	address: Address object (only applicable for DELIVERY),
	specialRequest: string,
	origin: “IOS|ANDROID” or NULL,
	test: boolean, default false, if true, it’s as if type == “TEST” but order will behave correctly as the intended type,
	fulfillmentTime: milliseconds since epoch time, time the customer wants to be fulfilled, if null, means NOW
	customer: Guest object, optional,
	phone: string, optional, if passed in sets the phone number that the merchant can call specifically for that order,
	vouchers: Array of Voucher IDs to be applied to this order,
	promoCodes: Array of promo codes to be applied to this order,
	rewardPoints: integer,
	pax: integer, number of people eating in (only applicable for DINE_IN)
}
</pre>

If type == “TEST” the server will return a response as if this order is successfully placed. Useful for retrieving taxes, applying promotion codes, vouchers and so on.

**Apps should not pass in any taxes and/or discounts from promotions. Those will automatically calculated and applied by Alpha when the order is posted.**

See <a href="#getting-all-promotions">here</a> for more details on applying promotions.
 
#### Guest:
<pre>
{
	firstName: optional
	lastName: optional,
	phone: optional,
	email: optional
}
</pre>

#### OrderItem:
<pre>
{
	id: ID, ignored when POSTed to endpoint
	product: ID of product,
	productName: string, name of product, ignored when POSTed to endpoint
	quantity: integer,
	modifiers: Array of ModifierItems,
	total: decimal, ignored when POSTed to endpoint
	specialRequest: string, instructions provided by user when ordering,
	variant: ID of Variant that is chosen, can be null for Products without Variants
	variantName: string name of Variant that was chosen, ignored when POSTed to endpoint,
	variantPrice: decimal number, price of variant chosen, if no variant was chosen, this will be null, ignored when POSTed to endpoint
}
</pre>

#### ModifierItem:
<pre>
{	
	id: ID, ignored when POSTed to endpoint
	modifierName: string, ignored when POSTed to endpoint
	modifierId: ID of Modifier this is referring to
	quantity: integer,
	modifiers: Array of ModifierItems (only applicable if this modifier is a combo product)
}
</pre>

#### Payment:
<pre>
{
	id: ID, ignored when POSTed to endpoint
	amount: decimal number,
	type: “CASH|CREDIT_CARD|PAYPAL|CREDITS”,
	transactionId: string, can be anything, this value will just be stored as information,
	paidTime: long, milliseconds since epoch time,
	status: “PAID|CANCELLED|REFUNDED”
}
</pre>

#### Address:
<pre>
{
	line1: string
	line2: string, optional
	city: string
	countryCode: 2 character string, ISO 3166-2 country code
	postalCode: string
}
</pre>

### Response:
<pre>
{
	success: true/false,
	order: Order object,
	vouchers: Array of VoucherErrors,
	promoCodes: Array of PromoCodeErrors

}
</pre>

When promotions are applied, success will be false if the any of the vouchers or promo codes applied are not valid. The `vouchers` property and `promoCodes` will contain details about why they are invalid.

#### VoucherError:
<pre>
{
	id: ID of Voucher that is invalid,
	message: string, a human readable string that explains why the voucher is invalid
}
</pre>

#### PromoCodeError:
<pre>
{
	code: string, the promo code that is invalid,
	message: string, a human readable string that explains why the promo code is invalid
}
</pre>

#### Order:
<pre>
{
	id: ID, ignored when POSTed to endpoint,
	type: “DELIVERY|TAKE_AWAY|DINE_IN|TEST”,
	number: string, human readable unique number of this order, ignored for TEST,
	items: Array of OrderItems,
	extraCharges: Array of ExtraCharges,
	payments: Array of Payments,
	discounts: Array of Discounts
	placeTime: long, milliseconds since epoch time, the time this order was placed,
	status: “PLACED|COMPLETED|CANCELLED|DELETED|READY”, see Order Status section,
	total: decimal, absolute final total, ignored when POSTed to endpoint,
	store: ID of store,
	table: ID of table,
	storeObject: Store object,
	tableObject: Table object,
	specialRequest: string,
	etaTime: long, milliseconds since epoch time, time the order will actually be fulfilled,
	fulfillmentTime: long, miliseconds, since epoch, time the customer wants the order to be fulfilled,
	phone: customer’s phone number, may be null,
	pax: number of people eating in
}
</pre>

#### ExtraCharge:
<pre>
{
	id: ID, ignored when POSTed to entpoint,
	percentage: decimal, can be NULL, for “20%”, this value will be = 20,
	type: “GST|SERVICE_CHARGE|DELIVERY_CHARGE|OTHERS”,
	comment: string,
	amount: decimal, absolute amount in dollars of this extra charge,
	name: name as specified by the admin if applicable,
	string: human friendly value to show to customers,
	tax: boolean, where this charge is a tax, apps may need to treat the display of taxes differently, also taxes should be displayed last,
	inclusive: boolean, whether this charge is already included in item price
}
</pre>

#### Discount:
<pre>
{
	id: ID, ignored when POSTed to endpoint,
	percentage: same idea from ExtraCharge’s percentage,
	amount: same,
	reason: string, why this discount is given
}
</pre>

#### Samples:

##### Posting a test order: `http://alpha.savantdegrees.com/api/1.0/order/ALT?authorization=jO0fHEBl1C2d0WOGGIJt6amAwI84msle`
<pre>
{
	"type": "TEST",
	"items": [ 
		{
			"product": 31,
			"quantity": 1,
			"modifiers": [
				{
					"modifierId": 610,
					"quantity": 1
				},
				{
					"modifierId": 606,
					"quantity": 3
				}
			]	
		},
		{
			"product": 4,
			"quantity": 1,
			"modifiers": [
				{
					"modifierId": 261,
					"quantity": 1
				},
				{
					"modifierId": 265,
					"quantity": 1
				},
				{
					"modifierId": 266,
					"quantity": 2
				}
			]
		},
		{
			"product": 273,
			"quantity": 1,
			"variant": 6
		}
	],
	"discounts": [
		{
			"amount": 2.99,
			"reason": "$2.99 off for using this cool app"
		}
	],
	"payments": [
		{
			"amount": 49.01,
			"type": "PAYPAL",
			"transactionId": "PAYPAL-123456",
			"status": "PAID",
			"paidTime": 1457273565769
		}
	],
	"store": 1
}
</pre>

## Reading all Orders

`http://<server>/api/1.0/orders/<brandCode>`


### GET Parameters:
* store: ID of Store, if missing Orders from all Stores are returned
* pageSize: maximum number of records to return per page, if missing = 20
* pageNumber: 0-based index of page to return, if missing = 0
* sortOrder: “ASC|DESC”, if missing = “ASC”
* sortProperty: “placeTime”, for now, if missing = “placeTime”
* status: “PLACED|COMPLETED|CANCELLED”, if missing, all orders will be returned
* placeTimeFrom: long, epoch time in millisecond, inclusive
* placeTimeTo: long, epoch time in millisecond, exclusive
* authToken: if passed in, will only return orders for this customer
* number: string, order number

### Response:
<pre>
{
	success: true,
	orders: Array of Order,
	pager: Pager object
}
</pre>

#### Pager:
<pre>
{
	count: integer, total number of records that match the query
	pageSize: integer, size (number of records) per page
	pageNumber: 0-based index of page
}
</pre>

## Order Statuses

When an order is submitted, its status is PLACED. The state transitions are as follows:

* PLACED -&gt; QUEUED | PREPARATION | CANCELLED
* QUEUED -&gt; PREPARATION | CANCELLED
* PREPARATION -&gt; READY | CANCELLED
* READY -&gt; COMPLETED |  CANCELLED
* COMPLETED -&gt; CANCELLED


The following are descriptions for each status (values in brackets are what is displayed to back-end users):

* **PLACED (Pending)**: Order is placed and pending acceptance
* **QUEUED (Pre-order)**: Order is queued to be prepared at a future time (for pre-orders, i.e. fulfillmentTime is not null). This happens when the order is accepted
* **PREPARATION (Preparing)**: Order is being prepared in the kitchen. This happens when the order is accepted (for ASAP orders), or when the order is close to fulfilment time (for pre-orders) i.e fulfilment time - delivery time - kitchen prep time
* **READY (Ready / In-Transit)**: Order is ready for serving or delivery. This happens automatically based on the kitchen preparation time set by the store administrator
* **COMPLETED**: Order is completed. This only happens when someone completes the order
* **CANCELLED**: Order is cancelled for some reason. An order can be cancelled at any time.

## Accepting an Order

`http://<server>/api/1.0/order/<brandCode>/accept/<order number>`

### Response:
<pre>
{
	success: true,
	order: Order object
}
</pre>

## Completing an Order

`http://<server>/api/1.0/orders/<brandCode>/complete/<order number>`

### Response:
<pre>
{
	success: true,
	order: Order object
}
</pre>

## Cancelling an Order

`http://<server>/api/1.0/orders/<brandCode>/cancel/<order number>`

### Response:
<pre>
{
	success: true,
	order: Order object
}
</pre>

## Register a Customer

`http://<server>/api/1.0/customer/register/<brandCode>`

*Don’t pass in the parameter if not available*

### POST Parameters:
* firstName
* lastName
* email
* password
* gender: “MALE|FEMALE”
* origin: “ANDROID|IOS”
* dateOfBirth: long value, epoch time in milliseconds
* phone
* facebookId
* googlePlusId
* lineId: LINE mid
* accessToken: Required when facebookId, googlePlusId or lineId is passed in
* refreshToken: lineId refresh token
* addressName: name of address, free-form value e.g. “Home”, “Office”
* addressLine1
* addressLine2
* addressCity
* addressCountryCode: 2-character string ISO 3166-2
* addressPostalCode
* deviceToken: device token for push notifications
* deviceType: “ANDROID|IOS”
* nationalityCode: 2-character string ISO 3166-2 country code

### Response:
<pre>
{
	success: true,
	authToken: string,
	code: integer
}
</pre>

#### code values:
* -100 : Email already exists
* -101:  Email already exists and account is connected to Facebook
* -102: Email already exists and account is connected to Google Plus
* -106: Email already exists and account is connected to LINE

## Update a Customer

`http://<server>/api/1.0/customer/update/<brandCode>`

*Don’t pass in the parameter if not updating.*

### Additional POST Parameters:
* authToken: authentication token of the user
* addressId: ID of address to update, if not passed in when address data is passed in the system will create a new address for the customer

The rest are same as register.

## Delete a Customer Address

`http://<server>/api/1.0/customer/deleteAddress/<brandCode>`

### POST Parameters:
* authToken: string
* id: ID of Address to delete

### Response:
<pre>
{
	success: true
}
</pre>

## Get Customer Details

`http://<server>/api/1.0/customer/details/<brandCode>`

### GET Parameters:
* authToken: string

### Response:
<pre>
{
	success: true
	customer: Customer Object
}
</pre>

#### Customer:
<pre>
{
	id: ID
	firstName
	lastName
	email
	phone
	googlePlusId
	facebookId
	gender: “MALE|FEMALE” or null 
	dateOfBirth: milliseconds since epoch time, or null
	addresses: List of Addresses
	rewards: Rewards object
	credits: decimal, number of credits the customer has left, should only be used for display
	countryCode: 2-character string country code,
	vouchers: Array of Vouchers this customer has in their account, this can be applied on an Order
}
</pre>

#### Voucher:
<pre>
{
	id: ID,
	promotion: Promotion object,
	used: integer, number of times this voucher has been used, only applicable for vouchers that can be used multiple times per customer
}
</pre>
	

#### Rewards:
<pre>
{
	totalRewardPoints
	expiryDate: miliseconds since epoch time, when the reward points will expire
	earnPointAmount
	earnPoint
	redeemPointAmount
	redeemPoint
	description
	earnExclusionCategories: List of Categories
	redeemExclusionCategories: List of Categories
}
</pre>

## Login Customer

`http://<server>/api/1.0/customer/login/<brandCode>`

### POST Parameters:
* email
* password
* facebookId
* googlePlusId
* lineId: LINE mid</li>
* accessToken: Facebook or Google Plus or LINE access token
* refreshToken: LINE refresh token
* deviceToken: device token for push notifications
* deviceType: “ANDROID|IOS”

(email and password) OR (facebookId and accessToken) OR (googlePlusId and accessToken) OR (lineId and accessToken) is required

### Response:
<pre>
{
	success: true,
	authToken: string
}
</pre>

## Logout Customer

`http://<server>/api/1.0/customer/logout/<brandCode>`

### POST Parameters:
* accessToken: Facebook or Google Plus access token
* authToken: string
* deviceToken: device push token so as to disable push notifications to this device

### Response:
<pre>
{
	success: true
}
</pre>

## Request for Customer Password Change

`http://<server>/api/1.0/customer/forgotPassword/<brandCode>`

### POST Parameters:
* email: customer email address

### Response:
<pre>
{
	success: true
}
</pre>

#### code values (if success: false)
* -103: Email not found

## Change Customer Password (after Request)

`http://<server>/api/1.0/customer/changePassword/<brandCode>`

### POST Parameters:
* email: customer email address
* pin: pin number that was sent to their email account
* password: new password

### Response:
<pre>
{
	success: true
}
</pre>

#### code values (if success: false)
* -103: Email not found
* -104: Pin is incorrect
* -105: Code has expired

## Get ETA

`http://<server>/api/1.0/eta/<brandCode>`

### GET Parameters:
* store: ID of store</li>
* orderType: “DELIVERY|TAKE_AWAY|DINE_IN”
* address: string, pass in either this or (postalCode and countryCode)
* postalCode: pass in (this and countryCode) or address
* countryCode: see postalCode
* subTotal: decimal number, defaults to 0 if not passed in


## Get GPS Coordinates from Address

`http://<server>/api/1.0/location/coordinates`

### GET Parameters:
* line: string, any free-form string containing some kind of address

### Response:
<pre>
{
	success: true,
	lat: latitude,
	lng: longitude
}
</pre>

## Get Address from GPS Coordinates

`http://<server>/api/1.0/location/address`

### GET Parameters:
* lat: double, latitude
* lng: double, longitude

### Response:
<pre>
{
	success: true,
	address: Address object
}
</pre>

## Get Closest Store from Address

`http://<server>/api/1.0/location/closestStore/<brandCode>`

### GET Parameters:
* line: address string

### Response:
<pre>
{
	success: true,
	store: Store object
}
</pre>

## Get Favourite Product List for Customer

`http://<server>/api/1.0/customer/favourites`

### GET Parameters:
* authToken: string
* productData: boolean, if true returns product details in 'favourites' property of the response

### Response:
<pre>
{
	success: true,
	favourites: Array of Favourites
}
</pre>

#### Favourite:
<pre>
{
	product: ID of product,
	time: milliseconds since epoch time, time this product was favourited,
	data: Product object, only sent when productData parameter is true
}
</pre>

## Add a Favourite Product for Customer

`http://<server>/api/1.0/customer/favourite`

### POST Parameters:
* authToken: string
* product: ID of product

### Response:
<pre>
{
	success: true
}
</pre>

## Remove a Favourite Product for Customer

`http://<server>/api/1.0/customer/unfavourite`

### POST Parameters:
* authToken: string
* product: ID of product

### Response:
<pre>
{
	success: true
}
</pre>

## Getting all Promotions

`http://<server>/api/1.0/promotions/<brandCode>`

Promotions are to be displayed only. There are two types of promotions - vouchers and promo codes. Vouchers are automatically given to customers in their account (see <a href="#customer">Customer</a> `vouchers` property). Customers can then choose which vouchers to apply into their orders. Promo codes on the other hand are free to be used at any time. They simply have to be entered into the order. Of course, terms and conditions still apply.

Apps should pass in vouchers and/or promo codes selected by the customer to Alpha with "test" = true, to query whether the promotion(s) can be applied. Alpha will return the order with discounts if the promotion(s) are valid. This should be presented to the customer before paying.

Note that payments will be not validated by Alpha. Users usually pay first before the app posts the order to Alpha. Therefore, apps **must** restrict the payment options when a promotion that requires only certain payments is applied.

### Response:
<pre>
{
	success: true,
	promotions: Array of Promotions
}
</pre>

#### Promotion:
<pre>
{
	id: ID,
	name: string,
	description: string,
	imageId: string,
	tnc: string, terms and conditions,
	benefitType: "FREE_ITEM|DOLLAR_DISCOUNT_ON_ITEM|DOLLAR_DISCOUNT_ON_CART|PERCENTAGE_DISCOUNT_ON_ITEM|PERCENTAGE_DISCOUNT_ON_CART", see Benefit Types below for more details,
	periodType: "LIMITED_PERIOD|FOREVER",
	periodFrom: millseconds since epochTime, when this promotion starts, only applicable when periodType == "LIMITED_PERIOD",
	periodFrom: millseconds since epochTime, last day before this promotion ends, only applicable when periodType == "LIMITED_PERIOD",
	fulfillmentType: "ANY_TIME|CUSTOM", ANY_TIME means this can be redeemed anytime during the promotion period, CUSTOM means can only be redeemed during the fulfillment times,
	fulfillmentTimes: Array of Availabilities, can only be redeemed during these hours, only applicable when fulfillmentType == "CUSTOM",
	publishType: "VOUCHERS|PROMO_CODE", 
	exclusive: boolean, if true this promotion can only be the only promotion used in an order,
	code: string, the promo code, only appicable when publsihType == "PROMO_CODE",
	freeType: "PRODUCT|MODIFIER", if PRODUCT the free item will be a specific product, if MODIFIER, it will be a specific modifier, only applicable when benefitType == "FREE_ITEM",
	freeProduct: ID of Product or null, only applicable when freeType == "PRODUCT",
	freeModifier: ID of Modifier or null, only applicable when freeType == "MODIFIER",
	takeAway: boolean, whether this promotion can be used for take away orders,
	dineIn: boolean, whether this promotion can be used for dine in orders,
	delivery: boolean, whether this promotion can be used for delivery orders,
	takeAwayStores: Array of Store IDs, only applicable when takeAway is true, and if empty, means ALL stores are applicable,
	dineInStores: Array of Store IDs, only applicable when dineIn is true, and if empty, means ALL stores are applicable,
	deliveryStores: Array of Store IDs, only applicable when delivery is true, and if empty, means ALL stores are applicable,
	emailDomains: Array of strings, e.g. of a string - "@eunoia.asia", only customers whose emails match any of these domains can apply this promotion,
	paymentTypes: Array of strings, currently only "CASH", "MASTERPASS", "PAYPAL", "STRIPE", only orders paid with any of these payment methods are applicable, if empty all payment types are applicable,
	dollarDiscount: decimal number, dollars off a product or cart, only applicable when benefitType == "DOLLAR_DISCOUNT_ON_ITEM" || "DOLLARY_DISCOUNT_ON_CART",
	percentageDiscount: decimal number, x% off off a product or cart, only applicable when benefitType == "PERCENTAGE_DISCOUNT_ON_ITEM" || "PERCENTAGE_DISCOUNT_ON_CART",
	criteriaProducts: Array of Product IDs, products that will receive dollar or percentage discount, only applicable when benefitType == "DOLLAR_DISCOUNT_ON_ITEM" || "PERCENTAGE_DISCOUNT_ON_ITEM",
	minAmountSpent: decimal number, minimum amount that must be spent (sub-total) before this promotion can be applied,
	maxDiscountValue: decimal number, maximum amount of discount that will be capped when applying this promotion,
	stockCount: "LIMITED|UNLIMITED", if LIMITED, this promotion can be only be redeemed stockLimit number of times,
	stockLimit: number of times this promotion can be redeemed, only applicable when stockCount == "LIMITED",
	usageCount: "LIMITED|UNLIMITED", if LIMITED, this promotion can be only be redeemed usageLimit number of times per customer,
	usageLimit: number of times this promotion can be redeemed by a customer, only applicable when usageCount == "LIMITED",
	redeemed: number of times this promotion has been redeemed by any customers, apps should simply not allow this promotion to be applied when this value is equal (or greater) than stockLimit
}
</pre>

#### Benefit Types

The following are the values of this enum:

* **FREE\_ITEM**: The benefit of this promotion is a free product or modifier. Apps should remind the user the item is not added to their order if it is not there. Otherwise, the server will simply return an error anyway.
* **DOLLAR\_DISCOUNT\_ON\_ITEM**: Dollar discount on a product that is in the criteria list of products
* **DOLLAR\_DISCOUNT\_ON\_CART**: Dollar discount on a whole order
* **PERCENTAGE\_DISCOUNT\_ON\_ITEM**: Percentage discount on a product that is in the criteria list of products
* **PERCENTAGE\_DISCOUNT\_ON\_CART**: Percentage discount on a whole order

## Testing Push Notifications

`http://<server>/api/1.0/test/<brandCode>/push`

### POST Parameters:
* message: string
* deviceType: "ANDROID|IOS"
* title: string
* deviceId: string

Configuration to enable this to work is under Brand > Edit under the Brands menu.

The following fields have to be entered for GCM push notifications to work: 

* Android Push Key

The following fields have to be entered for APN push notifications to work:
 
* Upload iOS Push Key File (.p12)
* iOS Push Key Password
* iOS Push Server Destination

# Collision 8 Extensions

## Get all Customers

`http://<server>/api/1.0/customers/<brandCode>`

### GET Parameters:
* pageSize: maximum number of records to return per page, if missing = 20
* pageNumber: 0-based index of page to return, if missing = 0
* memberId: string, optional, if passed in, only the member with that memberId is returned

### Response:
<pre>
{
	success: true,
	members: Array of Customers
}
</pre>

#### Customer (extended properties):
<pre>
{
	memberId: string,
	pin: string, not used until phase 2, probably as NFC id
	credits: decimal, credit balance
} 
</pre>

## Posting an Order

* Same end-point. However, don’t pass in any payments.  The system will create a payment of type “CREDITS” and deduct the order total from the member’s credit balance. 
* “type” property should be “TAKE_AWAY”.
* “memberId” is required.

### Example:
<pre>
{
    "type": "TAKE_AWAY",
    "items": [ 
        {
            "product": 1,
            "quantity": 2
        }
    ],
    "store": 1,
    "memberId": "S8431393I"
}
</pre>

# MasterDemo Extensions

All requests to MasterDemo API should include the `language` parameter. This is a 2 letter ISO 639-1 code that will return the content in that language.

The Brand object has the following additional properties:

#### Brand:
<pre>
{
	colors: Color object,
	minAuthAmount: decimal number, if this is greater than zero, any cart total larger than this value will require authentication before payment
}
</pre>

#### Color:
<pre>
{
	backgroundHeader: string, hexadecimal color code, eg “#123456”, can be null, this is the color code for the background color of the header bar on the app
	header: string, color of page title and icon, also the border color of the language dropdown
	highlight: string, background color for the counter, top bar, buttons and icon and text colors for the active page
	categoryBackground: string, background color for categories and subtitles
	contentBackground: string, background color for content
	bottomBarBackground: string, background color for bottom bar
}
</pre>

## Get App Translation Dictionary

`http://mastercard.savantdegrees.com/api/1.0/translate/masterdemo/dictionary`

### GET Parameters:
* language: "en|zh|id|jp", default is "en"

### Response:

A map of translation key-values. E.g.

<pre>
{
	"sign.in": "登入",
	"email": "電子郵件",
	"password": "密碼",
	"forgot.password": "忘記密碼",
	"registered": "註冊",
	"sign.in.facebook": "使用 Facebook 登錄",
	"sign.in.line": "使用 LINE 登錄"
}
</pre>

If a key is missing from the response, the app should display `&lt;key&gt;`. For example, if the translation for “email” is missing, the app should display `&lt;email&gt;`. This makes it obvious that a translation is missing, so it can be added to the API.

## Appending Items, Payments, and Promotions to an Existing Order

`http://mastercard.savantdegrees.com/api/1.0/order/<brandCode>/append/<orderNumber>`

### POST body:
<pre>
{
	items: Array of OrderItems,
	payments: Array of Payments,
	authToken: string, authentication token of current customer, if this is passed in, it will replace the current customer to this one,
	voucers: Array of Voucher IDs to be applied to this order,
	promoCodes: Array of promo codes to be applied to this order
}
</pre>

### Response:
<pre>
{
	success: true/false,
	order: Order object,
	vouchers: Array of VoucherErrors,
	promoCodes: Array of PromoCodeErrors
}
</pre>

## Generating an QR Code with an Order Number

`http://mastercard.savantdegrees.com/api/1.0/test/mastercard/qrCode`

### GET Parameters:
* payload: string, the content to embed in the QR code, should pass in the order number
* size: integer, width in pixels of the QR code image that should be generated. The image will be a square

### Response:
An JPEG image. 

## Querying whether an Order is Paid

`http://mastercard.savantdegrees.com/api/1.0/order/<brandCode>/paid/<orderNumber>`

### Response:
<pre>
{
	success: true,
	paid: boolean
}
</pre>

# Cama Cafe Extensions

#### Product:
<pre>
{
	priceDescription: string, can be null, describes why the price is that value, this is to inform the customer when there is a discount, if null don’t show this value
}
</pre>

#### Modifier Group:
<pre>
{
	selectionWarning: string, can be null, if not null, apps should display this value in as a warning when any modifiers in this group is selected by the user
}
</pre>

## Taiwan Addresses

Taiwan is divided into postal zones. Each postal zone is divided into divisions. Each division has a postal code. Road names are in divisions.

Apps should submit fields for delivery addresses like these:

* line1: Road name (retrieved by API) followed by the custom avenue or street name that is entered by the user
* line2: Division name (retrieved by API)
* city: Postal zone name (retrieved by API)
* postalCode: 3 character postal code of the division, see Get Divisions


## Get Postal Zones

`http://camacafe.savantdegrees.com/api/1.0/location/zones`

### Response:
<pre>
{
	success: true,
	zones: Array of Zones
}
</pre>

#### Zone:
<pre>
{
	id: ID,
	name: string
}
</pre>

## Get Divisions

`http://camacafe.savantdegrees.com/api/1.0/location/divisions`

### GET Parameters:
* id: integer, ID of the Zone to get the Divisions within that Zone

### Response:
<pre>
{
	success: true,
	divisions: Array of Divisions
}
</pre>

#### Division:
<pre>
{
	id: ID, note this is also the postal code of any address within this division
	name: string
}
</pre>

## Get Roads

`http://camacafe.savantdegrees.com/api/1.0/location/roads`

### GET Parameters:
* id: integer, ID of the Division to get the Roads within that Division

### Response:
<pre>
{
	success: true,
	divisions: Array of Roads
}
</pre>

#### Road:
<pre>
{
	id: ID,
	name: string
}
</pre>


## Get App Translation Dictionary

`http://camacafe.savantdegrees.com/api/1.0/translate/cama/dictionary`

### Response:

A map of translation key-values. E.g.
<pre>
{
	"sign.in": "登入",
	"email": "電子郵件",
	"password": "密碼",
	"forgot.password": "忘記密碼",
	"registered": "註冊",
	"sign.in.facebook": "使用 Facebook 登錄",
	"sign.in.line": "使用 LINE 登錄"
}
</pre>

If a key is missing from the response, the app should display `&lt;key&gt;`. For example, if the translation for “email” is missing, the app should display `&lt;email&gt;`. This makes it obvious that a translation is missing, so it can be added to the API.

# The Soup Spoon Extensions

These objects has the following additional properties:

#### Brand:
<pre>
{
	spendPoints: decimal number, for every multiple of this value used, the customer gets to redeem "redeemDollars",
	redeemDollars: decimal number, see "spendPoints"
}
</pre>

For example, given the following values:

* spendPoints = 100
* redeemDollars = 2

If a customer redeems 300 points, the customer gets $6 off the order. `300 / 100 * 2 = 6`

Apps can use this to calculate and show in real-time how many dollars discount the customer will get without having to request to the server whenever the customer is changing the points.

### Customer:
<pre>
{
	title: string, designation of the customer, "Mr", "Mrs", etc. This is a free-form string,
	souperholic: SouperholicData object
}
</pre>

### SouperholicData:
<pre>
{
	verified: boolean, whether the customer has clicked on the verification link sent by Souperholic, if this is not true, customers should not be allowed to register a card and redeem points,
	accountId: string, primary key of the customer account in Souperholic,
	card: string, the barcode number of the customer's Souperholic card, can be null if the customer doesn't have one,
	cardImage: string, a URL which is an barcode image of the card, apps can simply show this URL, can be null,
	deactivated: boolean, whether the customer's Souperholic card has been deactivated, i.e. can't earn or redeem points anymore,
	points: integer, number of points the customer has that can be redeemed,
	registeredDate: milliseconds since epochTime, time the Souperholic card was registered, can be null if the customer doesn't have one,
	expiryDate: milliseconds since epochTime, time the Souperholic card will expire, can be null if the customer doesn't have one
}
</pre>

Souperholic is a live Soup Spoon app that customers can use to earn and redeem points. See <http://www.thesoupspoon.com/souperholic-main/> This new app will integrate with Souperholic such that it will handle the points earning and redemption. That way customers can also login using the same account on the Souperholic app to see their points.

Souperholic requires the following fields upon registration:
* title
* firstName
* lastName
* dateOfBirth
* phone: must be an 8 digit number
* email

Failure to enter any of these fields will result an error. Note that emails and phone numbers are unique at Souperholic. Once one is registered, it cannot be registered again. Prepare multiple email accounts and multiple random phone numbers for QA!

## Redeeming Points

Apps should pass in the points the customer intends to redeem using the `rewardPoints` property in the Order JSON in the POST body of Posting an Order API. Passing in a test order works correctly, discounts will be created automatically with the correct amount. If this value is 0 or missing, Alpha considers the customer does not intend to redeem any points. In that case, if the order is eligible for points earning, points will be automatically credited to the customer's account. Apps should retrieve customer details again at the cart page, to get the latest points that the customer has before showing the shopping cart so the customer knows how many points is available for redemption.

The amount of points in the customer balance will be returned in the `pointsBalance` property whenever an Order is posted.

## Resend Souperholic Verification Email

`http://<server>/api/1.0/customer/souperResendVerification`

### POST Parameters:
* authToken: string

This will trigger a resend of the Souperholic verification email to the customer. Note that once a customer registers an account on Alpha, an account is also created at Souperholic which sends a verification email to the customer. This API call merely resends the same email. Also, note that if the user has not verified their Souperholic account, they cannot register a Souperholic card or earn and redeem points. Apps should not ask customers how many points they want to redeem if their account is not verified. See `SouperholicData.verified` property.

## Manually Verify Souperholic Email

`http://<server>/api/1.0/customer/souperVerify`

### POST Parameters:
* accountId: string, as contained in the Souperholic email
* verificationKey: string, as contained in the Souperholic email

Since the Souperholic server is a staging server, the verification email it sends out does not actually contain a link for the customer to verify their account. In that case, call this URL manually with the 2 parameters in the email, to verify the Souperholic account. Check that the account is verified by calling the Get Customer Details API.

## Register a Card 

`http://<server>/api/1.0/customer/souperRegisterCard`

### POST Parameters:
* authToken: string
* cardNumber: string, this is the barcode number or the number entered by the customer to register an existing card into their Souperholic account. If not passed in, a new card will be created

### Response:
<pre>
{
	success: true,
	souper: SouperholicData object
}
</pre>

