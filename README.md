# History of Changes

* 24 Oct 2016 - Added tax property to ExtraCharge
* 21 Oct 2016 - Added LINE properties to Customer Registration and Login API
* 23 Sep 2016 - Added authentication 
* 18 July 2016 - Added Collision 8 Extension
* 15 July 2016 - Added CREDITS as a new payment type
* 11 July 2016 - Added enableProductImages property to Brand
* 5 July 2016 - Added defaultImageId property to Brand
* 1 July 2016 - Added information regarding auth tokens in the overview section, added type property to tag
* 9 June 2016 - Added deviceToken property to Customer Registration and Login API, added Customer Logout API
* 6 June 2016 - Added showAll property to Getting the Menu API
* 1 June 2016 - Added addressId property to Update a Customer API
* 24 May 2016 - Added accessToken property to Login and Register a Customer, added Accepting an Order and Cancelling an Order, added productData parameter to Get Favourite Product List
* 23 May 2016 - Added code property to Register a Customer, added number property to Reading all Orders
* 7 Apr 2016 - Added brand property to menu API
* 6 Apr 2016 - Added tags property to documentation
* 4 Apr 2016 - Added imageId to store API
* 24 Mar 2016 - Added store specific menu
* 10 Mar 2016 - Add store dine-in, take away, opening and deliver hours. Added special request to order item. Fixed getting stores API, to return proper structure { success, stores }
* 8 Mar 2016 - Added Tables to Stores, added Table to Order, Added filters and sorting to Orders request


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


# Calling API Basics

It will be at something like `/api/<version>/<service>/<brandCode>`

* GET for reading
* POST for modifying something
* Response is always a JSON
* If success, response will be `{ status: true [, code: <optional unique integer> ], property1: etc, property2: etc }`
* If failure, it will be `{ status: false, message: “Hopefully some reason why it failed” [, code: <optional unique integer> ] }`
* All entities will have the property `id`. It is a long value that is only unique for that entity
* Images will have the property `imageId`. The value will be something like “bidfc6b9do2ugqf9jcfg” This is an ID to a resource on Cloudinary. It can also be null. The URL for the image will be `http://res.cloudinary.com/savantdegrees/image/upload/c_<crop-type>,h_<height>,w_<width/<id>` E.g <http://res.cloudinary.com/savantdegrees/image/upload/c_fill,h_200,w_200/bidfc6b9do2ugqf9jcfg> There are many ways to customize the image, get different dimensions and weird transformations. See <http://cloudinary.com/documentation/image_transformations>
* For customer related APIs, an `authToken` is required (see Login Customer)
* Any customer related requests with an invalid authToken will return `{ success: false, code: -666, message: “Some message you can display to the user” }`. The user should be redirected to the login flow
* If the API Authentication Key is set at a brand, every API request to that brand must pass in that key. The key can be passed in as a request parameter “authorization” or a request header “Authorization”. E.g. <http://alpha.savantdegrees.com/api/1.0/menu/ALT?authorization=jO0fHEBl1C2d0WOGGIJt6amAwI84msle>
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

**Store**:
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
	imageId: string,
	deliveryMaxOrderValue: decimal number or null, apps should not allow a delivery order larger than this value
	deliveryMinOrderValue: decimal number or null, apps should not allow a delivery order smaller than this value
}
</pre>


**Address**:
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


**Coordinates**:
<pre>
{
	lat: double, latitude
	lng: double, longitude
}
</pre>


**Table**:
<pre>
{
	id: ID,
	number: string, human readable number
}
</pre>

Sample: <http://alpha.savantdegrees.com/api/1.0/stores/ALT>

## Getting the Menu

`http://<server>/api/1.0/menu/<brandCode>`


### GET Parameters:
<pre>
<b>showAll</b>: boolean, default is false, if true returns all products regardless 
whether they are available or not
</pre>

Response: 
{
	success: true,
	categories: Array of Categories,
	products: Array of Products,
	tags: Array of Tags,
	brand: Brand object
	banners: Array of Banners,
}


Brand:
{
	id: ID,
	imageId: string,
	name: string,
	apiCode: string,
	defaultImageId: string, default product image the client should use if any
		product does not have an image, beware this can be null also
	enableProductImages: boolean
}


Tag: 
{
	id: ID
	imageId: string,
	name: string,
	type: string
}


Category:
{
	id: ID,
	imageId: string,
	availabilities: Array of Availabilities, if empty, this Category is available 
the whole day,
	name: string,
	description: string,
sortIndex: integer, can be used for sorting Categories in ascending order,
children: Array of child Categories
}


Product:
{
	id: ID,
	imageId: string,
	name: string,
	description: string,
	sortIndex: integer, can be used for sorting Products in ascending order,
	price: decimal number,
	dineIn: boolean, whether this Product is available for dine-in right now,
	delivery: boolean, whether this Product is available for delivery right now,
	takeAway: boolean, whether this Product is available for take away right now,
	categories: Array of Category IDs
	modifierGroups: Array of Modifier Groups,
	tags: Array of Tag IDs,
	Variants: Array of Variants
}


Modifier Group:
{
	id: ID,
	name: string,
	description: string,
	sortIndex: integer, can be used for sorting Modifier Groups in ascending
 order,
	combo: boolean, whether this group contains a combo of other Products,
	minModifiers: integer, minimum number of Modifiers that must be selected for 
this group,
	maxModifiers: integer, maximum number of Modifiers that can be selected for
this group,
	modifiers: Array of Modifiers
}


Modifier:
{
	id: ID,
	name: string,
	description: string,
	sortIndex: integer, can be used for sorting Modifiers in ascending order,
	price: decimal number,
	minQuantity: minimum number of this Modifier that must be selected,
	maxQuantity: maximum number of this Modifier that can be selected,
product: ID of a Product if this Modifier belongs in a Modifier Group that is 
a combo, otherwise null
	overridePrice: boolean, only applicable if value of product property is not 
null, if true the price of this Modifier is the value of price property, 
otherwise the price of this Modifier should be the price of the product,
	preSelected: boolean, whether this Modifier should already be selected for the 
		customer, of course the customer may end up selecting another Modifier
}


Availability:
{
	dayOfWeek: “MONDAY|TUESDAY|etc..”,
	startTime: string, format “HH:mm:ss.ms”,
	endTime: string, format same as above
}


Banner:
{
	id: ID,
	imageId: string,
	product: ID of product this banner is for, or null,
	sortIndex: integer,
	link: URL string or null,
	name: string,
	startTime: milliseconds since epochTime, time to start showing this banner,
	endTime: milliseconds since epochTime, time to stop showing this banner,
	target: “PRODUCT|LINK”,
	position: ”TOP|BOTTOM” 
}


Variant:
{
	id: ID,
	price: decimal number,
	modifierGroups: Array of Modifier Groups,
	name: string
}




Sample:
 http://alpha.savantdegrees.com/api/1.0/menu/ALT
 http://alpha.savantdegrees.com/api/1.0/menu/MLP
 http://alpha.savantdegrees.com/api/1.0/menu/MLP/BEDOK


For 2 and 3, notice that the same product ID: 276, has different names and prices. Also at BEDOK, only takeaway is available for that product.

## Posting an Order


`http://<server>/api/1.0/order/<brandCode>`


POST body:
{
	type: “DELIVERY|TAKE_AWAY|DINE_IN|TEST”,
	items: Array of OrderItems,
	payments: Array of Payments,
	discounts: Array of Discounts,
	store: ID of store that is receiving this order,
	table: ID of table for this order, can be NULL,
	authToken: authentication token of current customer (if null or missing, this
 is a guest order),
	address: Address object (only applicable for DELIVERY),
	specialRequest: string,
	origin: “IOS|ANDROID” or NULL,
test: boolean, default false, if true, it’s as if type == “TEST” but order 
will behave correctly as the intended type,
fulfillmentTime: milliseconds since epoch time, time the customer wants to be
 fulfilled, if null, means NOW
customer: Guest object, optional,
phone: string, optional, if passed in sets the phone number that the merchant 
	can call specifically for that order
voucher: ID of the voucher instance assigned to the customer,
rewardPoints: integer,
pax: integer, number of people eating in (only applicable for DINE_IN)
}


If type == “TEST” the server will return a response as if this order is successfully placed. Useful for retrieving taxes and so on.
 


Guest:
{
	firstName: optional
	lastName: optional,
	phone: optional,
	email: optional
}


OrderItem:
{
	id: ID, ignored when POSTed to endpoint
	product: integer, ID of product,
	productName: string, name of product, ignored when POSTed to endpoint
	quantity: integer,
	modifiers: Array of ModifierItems,
	total: decimal, ignored when POSTed to endpoint
specialRequest: string, instructions provided by user when ordering,
variant: ID of Variant that is chosen, can be null for Products without 
Variants
variantName: string name of Variant that was chosen, ignored when POSTed to 
	endpoint
}


ModifierItem:
{	
	id: ID, ignored when POSTed to endpoint
	modifierName: string, ignored when POSTed to endpoint
	modifierId: ID of Modifier this is referring to
	quantity: integer,
	modifiers: Array of ModifierItems (only applicable if this modifier is a combo 
product)
}


Payment:
{
	id: ID, ignored when POSTed to endpoint
	amount: decimal number,
	type: “CASH|CREDIT_CARD|PAYPAL|CREDITS”,
	transactionId: string, can be anything, this value will just be stored as 
information,
	paidTime: long, milliseconds since epoch time,
	status: “PAID|CANCELLED|REFUNDED”
}


Address:
{
	line1
	line2
	city
	countryCode
	postalCode
}


Response:
{
	success: true,
	order: Order object
}


Order:
{
	id: ID, ignored when POSTed to endpoint,
	type: “DELIVERY|TAKE_AWAY|DINE_IN|TEST”,
	number: string, human readable unique number of this order, ignored for TEST,
	items: Array of OrderItems,
	extraCharges: Array of ExtraCharges,
	payments: Array of Payments,
	discounts: Array of Discounts
	placeTime: long, milliseconds since epoch time, the time this order was 
placed,
	status: “PLACED|COMPLETED|CANCELLED|DELETED|READY”, see Order Status section,
	total: decimal, absolute final total, ignored when POSTed to endpoint,
	store: ID of store,
	table: ID of table,
	storeObject: Store object,
	tableObject: Table object,
	specialRequest: string,
	etaTime: long, milliseconds since epoch time, time the order will actually be 
fulfilled,
	fulfillmentTime: long, miliseconds, since epoch, time the customer wants the 
order to be fulfilled,
phone: customer’s phone number, may be null,
pax: number of people eating in
}


ExtraCharge:
{
	id: ID, ignored when POSTed to entpoint,
	percentage: decimal, can be NULL, for “20%”, this value will be = 20,
	type: “GST|SERVICE_CHARGE|DELIVERY_CHARGE|OTHERS”,
	comment: string,
amount: decimal, absolute amount in dollars of this extra charge,
name: name as specified by the admin if applicable,
string: human friendly value to show to customers,
tax: boolean, where this charge is a tax, apps may need to treat the display 
of taxes differently, also taxes should be displayed last
}


Discount:
{
	id: ID, ignored when POSTed to endpoint,
	percentage: same idea from ExtraCharge’s percentage,
	amount: same,
	reason: string, why this discount is given
}


Samples:


Posting a test order:
http://alpha.savantdegrees.com/api/1.0/order/ALT
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
		}
	],
	"discounts": [
		{
           		"amount": 9.99,
			"reason": "$9.99 off all bills promotion"
		}
	],
	"payments": [
	    {
	       "amount": 33.88,
	       "type": "PAYPAL",
	       "transactionId": "PAYPAL-123456",
	       "status": "PAID",
	       "paidTime": 1457273565769
	   }
	],
	"store": 1
}

## Reading all Orders


`http://<server>/api/1.0/orders/<brandCode>`


Parameters:
store: ID of Store, if missing Orders from all Stores are returned
pageSize: maximum number of records to return per page, if missing = 20
pageNumber: 0-based index of page to return, if missing = 0
sortOrder: “ASC|DESC”, if missing = “ASC”
sortProperty: “placeTime”, for now, if missing = “placeTime”
status: “PLACED|COMPLETED|CANCELLED”, if missing, all orders will be returned
placeTimeFrom: long, epoch time in millisecond, inclusive
placeTimeTo: long, epoch time in millisecond, exclusive
authToken: if passed in, will only return orders for this customer
number: string, order number


Response:
{
	success: true,
	orders: Array of Order,
	pager: Pager object
}


Pager:
{
	count: integer, total number of records that match the query
	pageSize: integer, size (number of records) per page
	pageNumber: 0-based index of page
}


## Order Statuses
When an order is submitted, its status is PLACED. The state transitions are as follows:


* PLACED -> QUEUED | PREPARATION | CANCELLED
* QUEUED -> PREPARATION | CANCELLED
* PREPARATION -> READY | CANCELLED
* READY -> COMPLETED |  CANCELLED
* COMPLETED -> CANCELLED


The following are descriptions for each status (values in brackets are what is displayed to back-end users):


PLACED (Pending): Order is placed and pending acceptance
QUEUED (Pre-order): Order is queued to be prepared at a future time (for pre-orders, i.e. fulfillmentTime is not null). This happens when the order is accepted
PREPARATION (Preparing): Order is being prepared in the kitchen. This happens when the order is accepted (for ASAP orders), or when the order is close to fulfilment time (for pre-orders) i.e fulfilment time - delivery time - kitchen prep time
READY (Ready / In-Transit): Order is ready for serving or delivery. This happens automatically based on the kitchen preparation time set by the store administrator
COMPLETED: Order is completed. This only happens when someone completes the order
CANCELLED: Order is cancelled for some reason. An order can be cancelled at any time.


## Accepting an Order


`http://<server>/api/1.0/order/<brandCode>/accept/<order number>`


Response:
```
{
	success: true,
	order: Order object
}
```

## Completing an Order


http://<server>/api/1.0/orders/<brandCode>/complete/<order number>


Response:
{
	success: true,
	order: Order object
}


Cancelling an Order


http://<server>/api/1.0/orders/<brandCode>/cancel/<order number>


Response:
{
	success: true,
	order: Order object
}


## Register a Customer


`http://<server>/api/1.0/customer/register/<brandCode>`


Don’t pass in the parameter if not available


Parameters:
firstName
lastName
email
password
gender: “MALE|FEMALE”
origin: “ANDROID|IOS”
dateOfBirth: long value, epoch time in milliseconds
phone
facebookId
googlePlusId
lineId: LINE mid
accessToken: Required when facebookId, googlePlusId or lineId is passed in
refreshToken: lineId refresh token
addressName: name of address, free-form value e.g. “Home”, “Office”
addressLine1
addressLine2
addressCity
addressCountryCode
addressPostalCode
deviceToken: device token for push notifications
deviceType: “ANDROID|IOS”
nationalityCode: 2-character string for a country code


Response:
{
	success: true,
	authToken: string,
	code: integer
}


code values:
-100 : Email already exists
-101:  Email already exists and account is connected to Facebook
-102: Email already exists and account is connected to Google Plus

## Update a Customer


`http://<server>/api/1.0/customer/update/<brandCode>`


Don’t pass in the parameter if not updating


Parameters:
authToken: authentication token of the user
addressId: ID of address to update, if not passed in when address data is passed in, 
	the system will try to find the address to update by its name


The rest are same as register. addressName if it matches an existing name of an address, will update that address, otherwise, will add a new address.


## Delete a Customer Address

`http://<server>/api/1.0/customer/deleteAddress/<brandCode>`


Parameters:
authToken: string
id: ID of Address to delete


Response:
{
	success: true
}

## Get Customer Details

`http://<server>/api/1.0/customer/details/<brandCode>`


Parameters:
authToken: string


Response:
{
	success: true
	customer: Customer Object
}


Customer:
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
credits: decimal, number of credits the customer has left, should only be used
	for displaying
couyntryCode: 2-character string for a country code
}


Rewards:
{
totalRewardPoints
expiryDate
earnPointAmount
earnPoint
redeemPointAmount
redeemPoint
description
earnExclusionCategories: List of Categories
redeemExclusionCategories: List of Categories
}


Address:
{
	id: ID
	name
line1
	line2
	city
	countryCode
	postalCode
	string: full one line display string of the address
}

## Login Customer


`http://<server>/api/1.0/customer/login/<brandCode>`


Parameters:
email
password
facebookId
googlePlusId
lineId: LINE mid
accessToken: Facebook or Google Plus or LINE access token
refreshToken: LINE refresh token
deviceToken: device token for push notifications
deviceType: “ANDROID|IOS”


(email and password) OR (facebookId and accessToken) OR (googlePlusId and accessToken) OR (lineId and accessToken) is required


Response:
{
	success: true,
	authToken: string
}


## Logout Customer


`http://<server>/api/1.0/customer/logout/<brandCode>`


Parameters:
accessToken: Facebook or Google Plus access token
deviceToken: device push token so as to disable push notifications to this device


Response:
{
	success: true
}


## Request for Customer Password Change


`http://<server>/api/1.0/customer/forgotPassword/<brandCode>`


Parameters:
email: customer email address


Response:
{
	success: true
}


code values (if success: false)
-103: Email not found

## Change Customer Password (after Request)


`http://<server>/api/1.0/customer/changePassword/<brandCode>`


Parameters:
email: customer email address
pin: pin number that was sent to their email account
password: new password


Response:
{
	success: true
}


code values (if success: false)
-103: Email not found
-104: Pin is incorrect
-105: Code has expired

## Get ETA


`http://<server>/api/1.0/eta/<brandCode>`


Parameters:
store: ID of store
orderType: “DELIVERY|TAKE_AWAY|DINE_IN”
address: string, pass in either this or (postalCode and countryCode)
postalCode: pass in (this and countryCode) or address
countryCode: see postalCode
subTotal: decimal number, defaults to 0 if not passed in


## Get GPS Coordinates from Address


`http://<server>/api/1.0/location/coordinates`


Parameters:
line: string, any free-form string containing some kind of address


Response:
{
	success: true,
	lat: latitude,
	lng: longitude
}

## Get Address from GPS Coordinates


`http://<server>/api/1.0/location/address`


Parameters:
lat: double, latitude
lng: double, longitude


Response:
{
	success: true,
	address: Address object
}


## Get Closest Store from Address


`http://<server>/api/1.0/location/closestStore/<brandCode>`


Parameters:
line: address string


Response:
{
	success: true,
	store: Store object
}


## Get Favourite Product List for Customer


`http://<server>/api/1.0/customer/favourites`


Parameters:
authToken: string
productData: boolean


Response:
{
	success: true,
	favourites: Array of Favourites
}


Favourite:
{
	product: ID of product,
	time: milliseconds since epoch time, time this product was favourited,
	data: Product object, only sent when productData parameter is true
}


## Add a Favourite Product for Customer


`http://<server>/api/1.0/customer/favourite`


Parameters:
authToken: string
product: ID of product


Response:
{
	success: true
}


## Remove a Favourite Product for Customer


`http://<server>/api/1.0/customer/unfavourite`


Parameters:
authToken: string
product: ID of product


Response:
{
	success: true
}


# Collision 8 Extensions

## Get all Customers
`http://<server>/api/1.0/customers/<brandCode>`


Parameters:
pageSize: maximum number of records to return per page, if missing = 20
pageNumber: 0-based index of page to return, if missing = 0
memberId: string, optional, if passed in, only the member with that memberId is
	 returned


Response:
{
	success: true,
	members: Array of Customers
}


Customer (extended properties):
{
	memberId: string,
	pin: string, not used until phase 2, probably as NFC id
	credits: decimal, credit balance
} 


Posting an Order
Same end-point. However, don’t pass in any payments.  The system will create a payment of type “CREDITS” and deduct the order total from the member’s credit balance. 


“type” property should be “TAKE_AWAY”.


“memberId” is required.


Example:
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


# MasterDemo Extensions


The Brand object has the following additional properties:


Brand: 
{
	colors: Color object
}


Color: 
{
backgroundHeader: string, hexadecimal color code, eg “#123456”, 
      can be null, this is the color code for the background 
      color of the header bar on the app
header: string, color of page title and icon, also the border 
       color of the language dropdown
highlight: string, background color for the counter, top bar, 
       buttons and icon and text colors for the active page
categoryBackground: string, background color for categories and 
       subtitles
contentBackground: string, background color for content
bottomBarBackground: string, background color for bottom bar
}

# Cama Cafe Extensions


Product:
{
	priceDescription: string, can be null, describes why the price is 
that value, this is to inform the customer when there is a 
discount, if null don’t show this value
}


## Get App Translation Dictionary


`http://camacafe.savantdegrees.com/api/1.0/translate/cama/dictionary`


Response:


A map of translation key-values. E.g.


{
	"sign.in": "登入",
	"email": "電子郵件",
	"password": "密碼",
	"forgot.password": "忘記密碼",
	"registered": "註冊",
	"sign.in.facebook": "使用 Facebook 登錄",
	"sign.in.line": "使用 LINE 登錄"
}


If a key is missing from the response, the app should display `<key>`. For example, if the translation for “email” is missing, the app should display `<email>`. This makes it obvious that a translation is missing, so it can be added to the API.
