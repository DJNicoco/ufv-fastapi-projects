# Assignment 2

Dockerfile Instructions:

Built a docker image:
docker build -t grocery-list-app .

Ran the docker container:
docker run -d -p 8000:8000 grocery-list-app
570ae30ab4dbec74027ef522e0ca51fc3bce8f83a477d55a945739c4a7a4a542

Confirmed that it ran:
docker ps
CONTAINER ID: 570ae30ab4db
IMAGE: grocery-list-app
COMMAND: "uvicorn fastapi_app…"        
CREATED: 22 seconds ago
STATUS: Up 21 seconds
PORTS: 0.0.0.0:8000->8000/tcp, :::8000->8000/tcp          
NAMES: festive_agnesi

## Tutorial 1: First Steps

my port 8000 outputted my message from main.py -> "Hello! This is my FastAPI App!"

Curl Command:
curl -s -X GET "http://127.0.0.1:8000/" -H "accept: application/json" | jq

JSON Output:
{
  "message": "Hello! This is my FastAPI App!"
}

## Tutorial 2: Path Parameters

I'm gonna do a grocery list with item_id in this tutorial.

Curl Command:
curl -X GET "http://127.0.0.1:8000/grocery/items/1" | jq

JSON Output after I asked for the first item in the list:
{
  "item_id": 1,
  "name": "Apples",
  "category": "Fruit",
  "price_per_gram": 3.99
}

## Tutorial 3: Query Parameters

I added items that filter by category and by min and max prices.
Note: I had to put the items_id coding below the category and price filtering so the FastAPI doesn't confuse the category or price with item_id.

Curl Command:
Filtering by category -> curl -X GET "http://127.0.0.1:8000/grocery/items/by-category?category=Fruit" | jq
curl -X GET "http://127.0.0.1:8000/grocery/items/by-category?category=Veggie" | jq

Filtering by max price -> curl -X GET "http://127.0.0.1:8000/grocery/items/by-price?max_price=3.00" | jq
Filtering by min price -> curl -X GET "http://127.0.0.1:8000/grocery/items/by-price?min_price=3.00" | jq
Filtering by prices between min and max prices -> curl -X GET "http://127.0.0.1:8000/grocery/items/by-price?min_price=2.50&max_price=3.50" | jq

JSON Output for category and price filtering:
Fruit Category:
{
  "1": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 3.99
  },
  "3": {
    "name": "Bananas",
    "category": "Fruit",
    "price_per_gram": 2.99
  }
}

Min price:
{
  "1": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 3.99
  },
  "2": {
    "name": "Celery",
    "category": "Veggie",
    "price_per_gram": 2.59
  },
  "3": {
    "name": "Bananas",
    "category": "Fruit",
    "price_per_gram": 2.99
  }
}

## Tutorial 4: Request Body

Here I added code where I can add items to the grocery list and it returns the whole list.

Curl Command:
curl -X POST "http://127.0.0.1:8000/grocery/items/"  -H "Content-Type: application/json" -d '{"name": "Milk", "category": "Dairy", "price_per_gram": 1.99}' | jq

JSON Output:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "name": "Milk",
    "category": "Dairy",
    "price_per_gram": 1.99
  }
}

## Tutorial 5: Query Parameters and String Validations

This is where I created a way to search for items on the grocery list 

Curl Command:
Name -> curl -X GET "http://127.0.0.1:8000/grocery/search/?name=Apple" | jq
Category -> curl -X GET "http://127.0.0.1:8000/grocery/search/?category=Fruit" | jq
Searching for a name and category: curl -X GET "http://127.0.0.1:8000/grocery/search/?name=Carrot&category=Veggie" | jq


JSON Output:
Name:
{
  "1": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 3.99
  },
  "3": {
    "name": "Bananas",
    "category": "Fruit",
    "price_per_gram": 2.99
  }
}

Category: 
{
  "2": {
    "name": "Celery",
    "category": "Veggie",
    "price_per_gram": 2.59
  }
}

Name and Category:
{
  "2": {
    "name": "Celery",
    "category": "Veggie",
    "price_per_gram": 2.59
  }
}

## Tutorial 6: Path Parameters and Numeric Validations

Here I find the item using their ID number.

Curl Commands:
curl -X GET "http://127.0.0.1:8000/grocery/items/2" | jq
curl -X GET "http://127.0.0.1:8000/grocery/items/4" | jq

JSON Output:
Item ID Number = 2
{
  "item_id": 2,
  "name": "Celery",
  "category": "Veggie",
  "price_per_gram": 2.59
}
Item ID Number = 4 (not on the list)
{
  "error message": "Item not found in the grocery list"
}

## Tutorial 7: Query Parameter Models

Here I added another pydantic model and I modified the search function to search for the min and max price.

Curl Commands:
curl -X GET "http://127.0.0.1:8000/grocery/search/" -H "Content-Type: application/json" -d '{"category": "Fruit", "max_price": 3.00}' | jq

JSON Output:
{
  "3": {
    "name": "Bananas",
    "category": "Fruit",
    "price_per_gram": 2.99
  }
}

## Tutorial 8: Body - Multiple Parameters

Here I created a function to add an item to the grocery list and to modify an existing item from the list.

Curl Commands:

An item is added:
curl -X POST "http://127.0.0.1:8000/grocery/items/" \
     -H "Content-Type: application/json" \
     -d '{"name": "Tomatoes", "category": "Veggie", "price_per_gram": 3.50}' | jq
     
An existing item is updated:
curl -X PUT "http://127.0.0.1:8000/grocery/items/2" \
     -H "Content-Type: application/json" \
     -d '{"name": "Celery", "category": "Veggie", "price_per_gram": 2.99}' | jq

JSON Output:
An item is added:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "name": "Tomatoes",
    "category": "Veggie",
    "price_per_gram": 3.5
  }
}

An existing item is updated:
{
  "message": "An item updated!",
  "item_id": 2,
  "item": {
    "name": "Celery",
    "category": "Veggie",
    "price_per_gram": 2.99
  }
}

## Tutorial 9: Body - Fields

Here I added Field to my GroceryItem Base Model for field validation. This will add grocery items as well.

Curl Commands:
curl -X POST "http://127.0.0.1:8000/grocery/items/" \
     -H "Content-Type: application/json" \
     -d '{"name": "Carrots", "category": "Veggie", "price_per_gram": 1.50}' | jq

JSON Output:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "name": "Carrots",
    "category": "Veggie",
    "price_per_gram": 1.5
  }
}

Full grocery list with all the items as well as the item I just added:
{
  "1": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 3.99
  },
  "2": {
    "name": "Celery",
    "category": "Veggie",
    "price_per_gram": 2.59
  },
  "3": {
    "name": "Bananas",
    "category": "Fruit",
    "price_per_gram": 2.99
  },
  "4": {
    "name": "Carrots",
    "category": "Veggie",
    "price_per_gram": 1.5
  }
}

## Tutorial 10: Body - Nested Models

For this tutorial, I added new grocery item details; weight, organic status, and availability.
I also implemented a total price calculation based on the item's weight and price per gram.

Curl Commands:
curl -X POST "http://127.0.0.1:8000/grocery/items/" -H "Content-Type: application/json" -d '{
       "name": "Carrots",
       "category": "Veggie",
       "price_per_gram": 1.50,
       "details": {
         "weight_grams": 30,
         "organic": true,
         "availability": "In stock"
       }
     }' | jq

JSON Output:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "name": "Carrots",
    "category": "Veggie",
    "price_per_gram": 1.5,
    "details": {
      "weight_grams": 30.0,
      "organic": true,
      "availability": "In stock"
    },
    "total price": 45.0
  }
}

## Tutorial 11: Declare Request Example Data

Here I added two examples, for carrots and strawberries, and added them both to the grocery list

Curl Commands:
curl -X 'POST' \ 'http://127.0.0.1:8000/grocery/items/' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "category": "Veggie",
  "details": {
    "availability": "In stock",
    "organic": true,
    "weight_grams": 30
  },
  "name": "Carrots",
  "price_per_gram": 1.5
}' | jq

curl -X 'POST' \ 'http://127.0.0.1:8000/grocery/items/' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{                       
  "category": "Fruit",
  "details": {
    "availability": "Out of Stock",
    "organic": false,
    "weight_grams": 15.0
  },        
  "name": "Strawberries",
  "price_per_gram": 0.50
}' | jq

JSON Output:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "name": "Carrots",
    "category": "Veggie",
    "price_per_gram": 1.5,
    "details": {
      "weight_grams": 30,
      "organic": true,
      "availability": "In stock"
    },
    "total price": 45.0
  }
}

{
  "message": "Item added!",
  "item_id": 5,
  "item": {
    "name": "Strawberries",
    "category": "Fruit",
    "price_per_gram": 0.50,
    "details": {
      "weight_grams": 15.0,
      "organic": false,
      "availability": "Out of Stock"
    },
    "total price": 7.50
  }
}

## Tutorial 12: Extra Data Types

Here I added some extra grocery item details; expiration date of the item, what time the item was added, and to show the id that seperates the item from the other items on the grocery list

Curl Command:
curl -X POST "http://127.0.0.1:8000/grocery/items/" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "Strawberries",
       "category": "Fruit",
       "price_per_gram": 0.50,
       "details": {
         "weight_grams": 15.0,
         "organic": true,
         "availability": "Seasonal",
         "expiration_date": "2025-03-15T10:00:00"
       }
     }' | jq

JSON Output:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "id": "6cf6ae3f-448e-40e1-b5c2-bbafe7314df6",
    "name": "Strawberries",
    "category": "Fruit",
    "price_per_gram": 0.5,
    "added_at": "2025-02-13T20:47:45.071717-08:00",
    "details": {
      "weight_grams": 15.0,
      "organic": true,
      "availability": "Seasonal",
      "expiration_date": "2025-03-15T10:00:00"
    },
    "total price": 7.5
  }
}

## Tutorial 13: Cookie Parameters

Here I added cookies to show that an item can be viewed and after it can check the last viewed item

Curl Commands:
Views an item -> curl -X GET "http://127.0.0.1:8000/grocery/items/2" -c cookies.txt | jq
Checks the last viewed item -> curl -X GET "http://127.0.0.1:8000/grocery/last-viewed/" -b cookies.txt | jq

JSON Outputs:
{
  "message": "Item found",
  "item_id": 2,
  "name": "Celery",
  "category": "Veggie",
  "price_per_gram": 2.59
}

{
  "message": "Your last viewed item is",
  "item": {
    "name": "Celery",
    "category": "Veggie",
    "price_per_gram": 2.59
  }
}

## Tutorial 14: Header Parameters

Here I added headers to add my own welcome message to the user who accessed my app, and to add a user role for the person who accessed an item

Curl Command:
curl -X GET "http://127.0.0.1:8000/grocery/items/1" -H "User-Role: Admin" | jq

JSON Output:
{
  "message": "Item found (Accessed by Admin)",
  "item_id": 1,
  "name": "Apples",
  "category": "Fruit",
  "price_per_gram": 3.99
}

## Tutorial 15: Cookie Parameter Models

Here I added a cookie parameter model; it has the last viewed item and the last searched item from the list and visit count for how many times an item is visited.

Curl Commands:

Sets a get request to search for bananas and stores the recieved cookies into cookies.txt:
curl -X 'GET' 'http://127.0.0.1:8000/grocery/search/?name=Bananas'   -H 'accept: application/json'   -c cookies.txt | jq

Sends a get request to search for apples, sends previously stored cookies, and updates the cookies and saves them back to cookies.txt:
curl -X 'GET' 'http://127.0.0.1:8000/grocery/search/?name=Apples' \
  -H 'accept: application/json' \
  -b cookies.txt -c cookies.txt | jq

Searches for celery, sends the updated cookies, and then saves the cookies
curl -X 'GET' 'http://127.0.0.1:8000/grocery/search/?name=Celery' \
  -H 'accept: application/json' \
  -b cookies.txt -c cookies.txt | jq

JSON Outputs:
Returns bananas and sets the cookie for the last searched item:
{
  "searched_items": {
    "3": {
      "name": "Bananas",
      "category": "Fruit",
      "price_per_gram": 2.99
    }
  },
  "last_searched_item": "Bananas",
  "previously_searched": "No previous searches.",
  "visit_count": 1
}

Returns apples, reads the previously set cookies, updates the last searched item and increments the visit count to 2 automatically:
{
  "searched_items": {
    "1": {
      "name": "Apples",
      "category": "Fruit",
      "price_per_gram": 3.99
    }
  },
  "last_searched_item": "Apples",
  "previously_searched": "Bananas",
  "visit_count": 2
}

Returns celery, reads previous cookies, updates the last searched item and increments the visit count to 3:
{
  "searched_items": {
    "2": {
      "name": "Celery",
      "category": "Veggie",
      "price_per_gram": 2.59
    }
  },
  "last_searched_item": "Celery",
  "previously_searched": "Apples",
  "visit_count": 3
}

## Tutorial 16: Header Parameter Models

Here I added a header parameter model to add a discount code.

Curl Commands:
This prints the grocery discount details:
curl -X 'GET' 'http://127.0.0.1:8000/grocery/discount' \
  -H 'accept: application/json' \
  -H 'discount-code: SAVE10' \
  -H 'user-role: Customer' | jq

This applies the discount and prints out the calculated discounted price:
curl -X GET "http://127.0.0.1:8000/grocery/items/1" \
  -H "accept: application/json" \
  -H "Discount-Code: SAVE10" \
  -H "User-Role: Customer" | jq

JSON Outputs:

Grocery discount details:
{
  "message": "Discount details accessed",
  "discount_code": "SAVE10",
  "discount_value": 10,
  "user_role": "Customer"
}

Discount is applied:
{
  "message": "Item found",
  "item_id": 1,
  "item": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 3.99
  },
  "previously_viewed": null,
  "discount_code": "SAVE10",
  "discount_value": 10,
  "original_price": 3.99,
  "discounted_price": 3.59
}

## Tutorial 17: Response Model - Return Type

Here I added a new response model, GroceryItemResponse. I modified get_grocery_item, get_all_grocery_items, and add_grocery_item

Curl Commands:

Get_grocery item (no discount):
curl -X 'GET' 'http://127.0.0.1:8000/grocery/items/1' | jq

Get_grocery item (with discount):
curl -X GET "http://127.0.0.1:8000/grocery/items/1" \
  -H "accept: application/json" \
  -H "Discount-Code: SAVE10" \ | jq

Get_all_grocery_items:
curl -X 'GET' 'http://127.0.0.1:8000/grocery/items/' | jq

Add_grocery_item:
curl -X 'POST' 'http://127.0.0.1:8000/grocery/items/' -H 'Content-Type: application/json' -d '{
  "name": "Carrot",
  "category": "Veggie",
  "price_per_gram": 0.05,
  "details": {
    "weight_grams": 30,
    "organic": true,
    "availability": "In stock"
  }
}' | jq

JSON Outputs:

Finds an item from the grocery list with no discount applied:
{
  "message": "Item found",
  "item_id": 1,
  "item": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 3.99
  },
  "previously_viewed": null,
  "discount_code": "No discount applied",
  "discount_value": null,
  "original_price": 3.99,
  "discounted_price": 3.99
}

Finds an item from the grocery list with a discount applied:
{
  "message": "Item found",
  "item_id": 1,
  "item": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 3.99
  },
  "previously_viewed": null,
  "discount_code": "SAVE10",
  "discount_value": 10,
  "original_price": 3.99,
  "discounted_price": 3.59
}

Prints all the grocery items:
{
  "1": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 3.99
  },
  "2": {
    "name": "Celery",
    "category": "Veggie",
    "price_per_gram": 2.59
  },
  "3": {
    "name": "Bananas",
    "category": "Fruit",
    "price_per_gram": 2.99
  },
  "4": {
    "id": "c47f611a-5fc1-4608-821a-fca8b030f8e0",
    "name": "Carrot",
    "category": "Veggie",
    "price_per_gram": 0.05,
    "added_at": "2025-02-14T21:12:36.791528-08:00",
    "details": {
      "weight_grams": 30.0,
      "organic": true,
      "availability": "In stock",
      "expiration_date": null
    },
    "total price": 1.5
  }
}

Add a grocery item:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "id": "c47f611a-5fc1-4608-821a-fca8b030f8e0",
    "name": "Carrot",
    "category": "Veggie",
    "price_per_gram": 0.05,
    "added_at": "2025-02-14T21:12:36.791528-08:00",
    "details": {
      "weight_grams": 30.0,
      "organic": true,
      "availability": "In stock",
      "expiration_date": null
    },
    "total price": 1.5
  }
}

## Tutorial 18: Extra Models

Here I added an extra model, to store extra details of the items like brand name and expiration date.
I modified update_grcery_item so metadata can be updated when modifying an item.
I modified search_grocery_item so users can filter items by brand.
I modified get_grocery_item so metadata will be included when getting an item.
I modified add_grocery_item so metadata will be saved when an item is added.

Curl Commands:
Modified apples which the metadata will now have:
curl -X 'PUT' 'http://127.0.0.1:8000/grocery/items/1' \
-H 'Content-Type: application/json' \                                                                                           -d '{                            
  "name": "Apples",
  "category": "Fruit",
  "price_per_gram": 1.20,
  "details": {
    "weight_grams": 10,
    "organic": true,
    "availability": "In stock"
  },
  "metadata": {
    "brand": "Organic Fruits",
    "expiration_date": "2025-02-20"
  }
}' | jq

The updated item will now have its brand and expiration date shown
curl -X 'GET' 'http://127.0.0.1:8000/grocery/items/1' -H 'accept: application/json' | jq

This item will have its metadata:
curl -X 'GET' 'http://127.0.0.1:8000/grocery/items/2' -H 'accept: application/json' | jq

Adding an item will get its metadata:
curl -X 'POST' 'http://127.0.0.1:8000/grocery/items/' -H 'Content-Type: application/json' -d '{
    "name": "Milk",
    "category": "Dairy",
    "price_per_gram": 0.20,
    "details": {
        "weight_grams": 50,
        "organic": true,
        "availability": "In stock"
    },
    "metadata": {
        "brand": "Organic Milk",
        "expiration_date": "2025-03-10"
    }
}' | jq


JSON Outputs:
Updated apples with all the metadata:
{
  "message": "An item is updated!",
  "item_id": 1,
  "item": {
    "id": "f878d3dc-ef61-468f-b8c1-ab2da1dd1240",
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 1.2,
    "added_at": "2025-02-15T17:39:00.227361-08:00",
    "details": {
      "weight_grams": 10.0,
      "organic": true,
      "availability": "In stock",
      "expiration_date": null
    },
    "metadata": {
      "brand": "Organic Fruits",
      "expiration_date": "2025-02-20"
    },
    "total_price": 12.0
  }
}

 Item was found with its brand:
{
  "message": "Item found",
  "item_id": 1,
  "item": {
    "id": "b5052440-2d1b-4cc2-9b2a-7e34c961cba2",
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 1.2,
    "added_at": "2025-02-15T18:41:22.320814-08:00",
    "details": {
      "weight_grams": 10.0,
      "organic": true,
      "availability": "In stock",
      "expiration_date": null
    },
    "metadata": {
      "brand": "Organic Fruits",
      "expiration_date": "2025-02-20"
    },
    "total_price": 12.0
  },
  "brand": null,
  "previously_viewed": null,
  "discount_code": "No discount applied",
  "discount_value": null,
  "original_price": 1.2,
  "discounted_price": 1.2,
  "total_price": 12.0
}

This item will have its metadata shown:
{
  "message": "Item found",
  "item_id": 2,
  "item": {
    "name": "Celery",
    "category": "Veggie",
    "price_per_gram": 2.59
  },
  "brand": null,
  "previously_viewed": null,
  "discount_code": "No discount applied",
  "discount_value": null,
  "original_price": 2.59,
  "discounted_price": 2.59,
  "total_price": 2.59
}

Adding an item will get its metadata:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "id": "a447db89-e0ff-422f-9949-9712ec29fc22",
    "name": "Milk",
    "category": "Dairy",
    "price_per_gram": 0.2,
    "added_at": "2025-02-15T19:26:12.710070-08:00",
    "details": {
      "weight_grams": 50.0,
      "organic": true,
      "availability": "In stock",
      "expiration_date": null
    },
    "metadata": {
      "brand": "Organic Milk",
      "expiration_date": "2025-03-10"
    }
  },
  "brand": null,
  "previously_viewed": null,
  "discount_code": "No discount applied",
  "discount_value": null,
  "original_price": 0.2,
  "discounted_price": 0.2,
  "total_price": 10.0
}

## Tutorial 19: Response Status Code

I modified get_grocery_item to return 404 for items not found. 
I modified add_grocery_item to return 201 created. 
I modified get_discount_details to return 400 for invalid discount.


Curl Commands:
Status code: 404 item not found
curl -X GET "http://127.0.0.1:8000/grocery/items/5" -H "accept: application/json" 
| jq

Status code: 201 created
curl -X POST "http://127.0.0.1:8000/grocery/items/" \
-H "Content-Type: application/json" \
-d '{
    "name": "Broccoli",
    "category": "Veggie",
    "price_per_gram": 2.00,
    "details": {
        "weight_grams": 25.0,
        "organic": true,
        "availability": "In stock"
    }
}' | jq

Status code: 400 Bad Request for invalid discount
curl -X GET "http://127.0.0.1:8000/grocery/discount" \
     -H "accept: application/json" \
     -H "Discount-Code: INVALIDCODE" \
     -H "User-Role: customer" | jq

JSON Output:
Status Code: 404 item not found
{
  "detail": "Item with ID 5 not found."
}

Status code: 201 created
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "id": "efd58339-fefd-4ee3-b184-44304774bb20",
    "name": "Broccoli",
    "category": "Veggie",
    "price_per_gram": 2.0,
    "added_at": "2025-02-15T20:33:51.613166-08:00",
    "details": {
      "weight_grams": 25.0,
      "organic": true,
      "availability": "In stock",
      "expiration_date": null
    },
    "metadata": null
  },
  "brand": null,
  "previously_viewed": null,
  "discount_code": "No discount applied",
  "discount_value": null,
  "original_price": 2.0,
  "discounted_price": 2.0,
  "total_price": 50.0
}

Status code: 400 Bad Request for invalid discount
{
  "detail": "Invalid discount code"
}

## Tutorial 20: Form Data

Here I modified add_grocery_list that accepts Form Data instead of JSON.

Curl Command:
This will send the data as Form Data:
curl -X POST "http://127.0.0.1:8000/grocery/items/" \
     -H "accept: application/json" \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -d "name=Grapes&category=Fruit&price_per_gram=0.10&weight_grams=20" | jq

Form Data Output:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "name": "Grapes",
    "category": "Fruit",
    "price_per_gram": 0.1,
    "details": {
      "weight_grams": 20.0
    }
  },
  "brand": null,
  "previously_viewed": null,
  "discount_code": "No discount applied",
  "discount_value": null,
  "original_price": 0.1,
  "discounted_price": 0.1,
  "total_price": 2.0
}

## Tutorial 21: Form Models

I created a form data model, GroceryItemForm.
I modified add_grocery_item to depend on the grocery item form and added a 
seperate function to create GroceryItemForm from form data.


Curl Command:
curl -X POST "http://127.0.0.1:8000/grocery/items/" \
     -H "accept: application/json" \
     -d "name=Grapes" \
     -d "category=Fruit" \
     -d "price_per_gram=0.10" \
     -d "weight_grams=20" | jq

JSON Output:
{
  "message": "Item added!",
  "item_id": 4,
  "item": {
    "name": "Grapes",
    "category": "Fruit",
    "price_per_gram": 0.1,
    "weight_grams": 20.0
  },
  "brand": null,
  "previously_viewed": null,
  "discount_code": "No discount applied",
  "discount_value": null,
  "original_price": 0.1,
  "discounted_price": 0.1,
  "total_price": 2.0
}

## Tutorial 22: Request Files

Here I added a function, upload_file, that can allow any file type for requesting files.
I first created a new file called testfile.txt and it got uploaded to uploaded_files and then ran the curl command below.

Curl Command
curl -X 'POST' \
  'http://127.0.0.1:8000/upload-file' \
  -H 'accept: application/json' \
  -F 'file=@testfile.txt' | jq

JSON Ouptut:
{
  "message": "File uploaded successfully",
  "filename": "testfile.txt",
  "content_type": "text/plain"
}

## Tutorial 23: Request Forms and Files

Here I modified upload_form_file to now read the file contents of the new saved file, testfile2.txt

Curl Command:
curl -X POST "http://127.0.0.1:8000/upload-form-file" \
     -F "description=This is my test file for tutorial 23" \
     -F "file=@uploaded_files/testfile2.txt" | jq

JSON Output:
{
  "message": "File and form data uploaded successfully",
  "filename": "testfile2.txt",
  "description": "This is my test file for tutorial 23",
  "content_type": "This is a test file for tutorial 23 request forms and files.\n"
}

I had to redo this one, I forgot to rename the function upload_file to upload_form_file in main.py.

## Tutorial 24: Handling Errors

Here I modified upload_form_file so it wil handle errors like checking for valid files, for empty file check, and an error if something unexpected will happen if a noreadablefile is not able to be read by fastapi.

Curl Commands:
This will check the valid file:
curl -X POST "http://127.0.0.1:8000/upload-form-file" \
     -F "description=This is my test file" \
     -F "file=@uploaded_files/testfile.txt" | jq

This will return the empty file error message:
curl -X POST "http://127.0.0.1:8000/upload-form-file" -F "description=This is the empty file" -F "file=@uploaded_files/emptyfile.txt" | jq

This will make the file readable by curl but not by fast api, sends a post request to upload the file to fast api, and now fast api will recieve the file but fail to read it:
chmod 400 uploaded_files/noreadfile.txt
curl -X POST "http://127.0.0.1:8000/upload-form-file" \
     -F "description=Testing unreadable file" \
     -F "file=@uploaded_files/noreadfile.txt" | jq

JSON Outputs:
This will check the valid file:
{
  "message": "File and form data uploaded successfully",
  "filename": "testfile.txt",
  "description": "This is my test file",
  "content_type": "This is a test file for tutorial 22 request files\n\nI also used this for handling errors, uploading a valid file\n"
}
 
This will return the empty file error message:
{
  "detail": "Uploaded file is empty"
}

This will trigger permission denied:
{
  "detail": "Unexpected error: [Errno 13] Permission denied: 'uploaded_files/noreadfile.txt'"
}

## Tutorial 25: Path Operation Configuration

Here I added path operation configuration to upload_form_file so it will have custom summaries and descriptions as well as customizing responses for testfile.txt, testfile2.txt, 
emptyfile.txt, and noreadfile.txt

Curl Commands:

Testing metadata after modifying upload_form_file for testfile.txt:
curl -X POST "http://127.0.0.1:8000/upload-form-file" \
     -F "description=Testing metadata" \
     -F "file=@uploaded_files/testfile.txt" | jq

Testing metadata after modifying upload_form_file for testfile2.txt:
curl -X POST "http://127.0.0.1:8000/upload-form-file" 
     -F "description=Testing metadata"  
     -F "file=@uploaded_files/testfile2.txt" | jq

Testing metadata after modifying upload_form_file for emptyfile.txt:
curl -X POST "http://127.0.0.1:8000/upload-form-file"      
     -F "description=Testing metadata"      
     -F "file=@uploaded_files/emptyfile.txt" | jq

Testing metadata after modifying upload_form_file for noreadfile.txt:
curl -X POST "http://127.0.0.1:8000/upload-form-file"      
     -F "description=Testing metadata"      
     -F "file=@uploaded_files/noreadfile.txt" | jq

JSON Response:
Testing metadata after modifying upload_form_file for testfile.txt:
{
  "message": "File and form data uploaded successfully",
  "filename": "testfile.txt",
  "description": "Testing metadata",
  "content": "This is a test file for tutorial 22 request files\n\nI also used this for handling errors, uploading a valid file\n"
}

Testing metadata after modifying upload_form_file for testfile2.txt:
{
  "message": "File and form data uploaded successfully",
  "filename": "testfile2.txt",
  "description": "Testing metadata",
  "content": "This is a test file for tutorial 23 request forms and files.\n"
}

Testing metadata after modifying upload_form_file for emptyfile.txt:
{
  "detail": "Uploaded file is empty"
}

Testing metadata after modifying upload_form_file for noreadfile.txt:
{
  "detail": "Unexpected error: [Errno 13] Permission denied: 'uploaded_files/noreadfile.txt'"
}

## Tutorial 26: JSON Compatible Encoder

Here I modified upload_form_file to make sure all return data is JSON compatible.

Curl Command:
curl -X POST "http://127.0.0.1:8000/upload-form-file" \
     -F "description=Testing JSON encoding" \
     -F "file=@uploaded_files/testfile.txt" | jq

JSON Output:
{
  "message": "File and form data uploaded successfully",
  "filename": "testfile.txt",
  "description": "Testing JSON encoding",
  "content": "This is a test file for tutorial 22 request files\n\nI also used this for handling errors, uploading a valid file\n"
}

## Tutorial 27: Body - Updates

Here I created two new base models, one for updating grocery items and the other for updating file metadata. 
I created a new text file, testfileforbodyupdates.txt, for this tutorial.
I modified update_grocery_item to use patch for updating items in the grocery list. 
I also modified upload_form_file to support metadata updates, I added a patch.

Curl Commands:
Updated items using PATCH:
curl -X PATCH "http://127.0.0.1:8000/grocery/items/1" \
     -H "Content-Type: application/json" \
     -d '{"price_per_gram": 0.02, "details": {"weight_grams": 15, "organic": true}}' | jq

Updates file metadata:
Step 1: did this to upload testfile.txt with metadata
curl -X POST "http://127.0.0.1:8000/upload-form-file" \
     -F "description=Testing file metadata storage" \
     -F "file=@uploaded_files/testfileforbodyupdates.txt" | jq
Step 2: did this to update the metadata
curl -X PATCH "http://127.0.0.1:8000/update-file/testfileforbodyupdates.txt" \
     -H "Content-Type: application/json" \
     -d '{"description": "New file description"}' | jq

JSON Outputs:
Updated items using PATCH:
{
  "message": "An item is updated!",
  "item_id": 1,
  "item": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 0.02,
    "details": {
      "weight_grams": 15,
      "organic": true
    },
    "total_price": 0.3
  }
}

Step 1 and 2 for updating file metadata:
{
  "message": "File and form data uploaded successfully",
  "filename": "testfileforbodyupdates.txt",
  "description": "Testing file metadata storage",
  "metadata": {
    "description": "Testing file metadata storage"
  },
  "content": "This for body updates testing patch"
}
{
  "filename": "testfileforbodyupdates.txt",
  "updated_metadata": {
    "description": "Updated file description"
  }
}

## Bonus: My Own Tutorial

First I added a new item, secondly I updated each item including the new item. After that it got saved to grocery_list.txt. I also added a function for deleting an item off the updated grocery list.

Curl Commands:

This will add a new item, I called it test milk since I will be deleting this one:
curl -X POST "http://127.0.0.1:8000/grocery/items/" -H "Content-Type: application/json" -d '{
  "name": "Test Milk",
  "category": "Dairy",
  "price_per_gram": 2.2,
  "weight_grams": 10,
  "organic": true
}' | jq

This updates item 1 with its own price_per_gram, weight_grams, and organic:
curl -X PATCH "http://127.0.0.1:8000/grocery/items/1" \
     -H "Content-Type: application/json" \
     -d '{"price_per_gram": 4.50, "details": {"weight_grams": 10, "organic": true}}' | jq

This updates item 2 with its own price_per_gram, weight_grams, and organic:
curl -X PATCH "http://127.0.0.1:8000/grocery/items/2" \
     -H "Content-Type: application/json" \
     -d '{"price_per_gram": 3.00, "details": {"weight_grams": 15, "organic": true}}' | jq

This updates item 3 with its own price_per_gram, weight_grams, and organic:
curl -X PATCH "http://127.0.0.1:8000/grocery/items/3" \
     -H "Content-Type: application/json" \
     -d '{"price_per_gram": 2.20, "details": {"weight_grams": 10, "organic": true}}' | jq

This updates item 4 with its own price_per_gram, weight_grams, and organic:
curl -X PATCH "http://127.0.0.1:8000/grocery/items/4" \
     -H "Content-Type: application/json" \
     -d '{"price_per_gram": 2.20, "details": {"weight_grams": 20, "organic": false}}' | jq

This saves the new updated items into the full gorcery list:
curl -X POST "http://127.0.0.1:8000/save-grocery-list" -H "accept: application/json" | jq

This will delete the test milk:
curl -X DELETE "http://127.0.0.1:8000/grocery/items/4?confirm=true"

This will confirm it:
cat grocery_list.txt

JSON Outputs:
This updates item 1 with its own price_per_gram, weight_grams, and organic:
{
  "message": "An item is updated!",
  "item_id": 1,
  "item": {
    "name": "Apples",
    "category": "Fruit",
    "price_per_gram": 4.5,
    "details": {
      "weight_grams": 10,
      "organic": true
    },
    "total_price": 45.0
  }
}

This updates item 2 with its own price_per_gram, weight_grams, and organic:
{
  "message": "An item is updated!",
  "item_id": 2,
  "item": {
    "name": "Celery",
    "category": "Veggie",
    "price_per_gram": 3.0,
    "details": {
      "weight_grams": 15,
      "organic": true
    },
    "total_price": 45.0
  }
}

This updates item 3 with its own price_per_gram, weight_grams, and organic:
{
  "message": "An item is updated!",
  "item_id": 3,
  "item": {
    "name": "Bananas",
    "category": "Fruit",
    "price_per_gram": 2.2,
    "details": {
      "weight_grams": 10,
      "organic": true
    },
    "total_price": 22.0
  }
}

This updates item 4 with its own price_per_gram, weight_grams, and organic:
{
  "message": "An item is updated!",
  "item_id": 4,
  "item": {
    "name": "Test Milk",
    "category": "Dairy",
    "price_per_gram": 2.2,
    "details": {
      "weight_grams": 20,
      "organic": false
    },
    "total_price": 44.0
  }
}

This saves the new updated items into the full gorcery list:
{
  "message": "Grocery list saved to grocery_list.txt"
}

This is the full grocery list with updated items:

Item ID: 1
Name: Apples
Category: Fruit
Price per gram: 4.5
Weight (grams): 10
Organic: True
Total price: 45.0

Item ID: 2
Name: Celery
Category: Veggie
Price per gram: 3.0
Weight (grams): 15
Organic: True
Total price: 45.0

Item ID: 3
Name: Bananas
Category: Fruit
Price per gram: 2.2
Weight (grams): 10
Organic: True
Total price: 22.0

This will delete the test milk:
{
  "message": "Item with ID 4 has been deleted successfully!",
  "deleted_item": {
    "item_id": 4,
    "name": "Test Milk",
    "category": "Dairy",
    "price_per_gram": 2.2,
    "details": {
      "weight_grams": 20,
      "organic": false
    },
    "total_price": 44.0
  }
}

This will confirm it:
Item ID: 1
Name: Apples
Category: Fruit
Price per gram: 4.5
Weight (grams): 10
Organic: True
Total price: 45.0

Item ID: 2
Name: Celery
Category: Veggie
Price per gram: 3.0
Weight (grams): 15
Organic: True
Total price: 45.0

Item ID: 3
Name: Bananas
Category: Fruit
Price per gram: 2.2
Weight (grams): 10
Organic: True
Total price: 22.0