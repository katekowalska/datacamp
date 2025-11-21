## PUT operation in action

You've been asked to create a PUT endpoint `/items` that accepts parameters `name` and `description` and updates the `description` based on the `name` in a key-value store called `items`.

You can't run the FastAPI server directly with "Run this file" - see the instructions for how to run the server and test your code from the terminal.

### Instructions

1. Define pydantic model `Item` so that parameters `name` and `description` can be passed into the PUT body.
2. Update `description` in `items` based on the key `name`.
3. Run the live server from the terminal: `fastapi dev main.py`.
4. Open a new terminal (top-right of terminal) and test your code with the following command:
```
curl -X PUT \
  -H 'Content-Type: application/json' \
  -d '{"name": "bananas", "description": "Delicious!"}' \
  http://localhost:8000/items
```

```python
main.py

from fastapi import FastAPI
from pydantic import BaseModel

# Define model Item
class Item(BaseModel):
    name: str
    description: str

# Define items at application start
items = {"bananas": "Yellow fruit."}

app = FastAPI()


@app.put("/items")
def update_item(item: Item):
    name = item.name
    # Update the description
    items[name] = item.description
    return item
```

## DELETE operation in action

You've been asked to create a DELETE endpoint that accepts parameter `name` and deletes the item called `name` from a key store called `items`.

You can't run the FastAPI server directly with "Run this file" - see the instructions for how to run the server and test your code from the terminal.

### Instructions

1. Define pydantic model `Item` with parameter `name`.
2. Delete from `items` based on the key `name`.
3. Run the live server from the terminal: `fastapi dev main.py`.
4. Open a new terminal (top-right of terminal) and test your code with the following command:

```
curl -X DELETE \
  -H 'Content-Type: application/json' \
  -d '{"name": "bananas"}' \
  http://localhost:8000/items
```

```python
main.py

from fastapi import FastAPI
from pydantic import BaseModel

# Define model Item
class Item(BaseModel):
    name: str

# Define items at application start
items = {"apples", "oranges", "bananas"}

app = FastAPI()


@app.delete("/items")
def delete_item(item: Item):
    name = item.name
    # Delete the item
    items.remove(name)
    return {}
```

## Status code classification

Classify the status codes by whether or not they indicate success.

| Success | Not Success |
| ------- | ------ |
| 200 | 400 |
| 201 | 404 |
| 202 | 500 |

## Handling a client error

You've been asked to create a DELETE endpoint that accepts parameter `name` and deletes the item called `name` from a key store called `items`. If the item is not found, the endpoint should return an appropriate status code and detailed message.

You can't run the FastAPI server directly with "Run this file" - see the instructions for how to run the server and test your code from the terminal.

### Instructions

1. Import `HTTPException` from `FastAPI`.
2. Raise `HTTPException` if an item is not in `items`.
3. Specify the appropriate status code for "not found."
4. Run the live server from the terminal: `fastapi dev main.py`.
5. Open a new terminal (top-right of terminal) and test your code with the following command:
```
curl -X DELETE \
  -H 'Content-Type: application/json' \
  -d '{"name": "bananas"}' \
  http://localhost:8000/items
```

```python
main.py

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

# Define model Item
class Item(BaseModel):
    name: str

# Define items at application startup
items = {"apples", "oranges"}

app = FastAPI()


@app.delete("/items")
def delete_item(item: Item):
    name = item.name
    if name in items:
        items.remove(name)
    else:
        # Raise HTTPException with status code for "not found"
        raise HTTPException(status_code=404, detail="Item not found.")
    return {}
```

## When should we use async?

:question: When should you use async in FastAPI?

:white_check_mark: When all the functions within our endpoint can be called with `await`

## Asynchronous DELETE operation

You've been asked to create an API endpoint that deletes items managed by your API. To accomplish this, create an endpoint `/items` that serves HTTP DELETE operations. Make the endpoint asynchronous, so that your application can continue to serve requests while maintaining any long-running deletion tasks.

We can't run the FastAPI server directly with "Run this file" - see the instructions for how to run the server and test your code from the terminal.

### Instructions

1. Make the delete operation asynchronous.
2. Validate the existence of `item.name` in list `items`.
3. Return the appropriate status code for "not found."
4. Run the live server from the terminal: `fastapi dev main.py`.
5. Open a new terminal (top-right of terminal) and test your code with the following command:
```
curl -X DELETE \
  -H 'Content-Type: application/json' \
  -d '{"name": "rock"}' \
  http://localhost:8000/items

curl -X DELETE \
  -H 'Content-Type: application/json' \
  -d '{"name": "roll"}' \
  http://localhost:8000/items
```

```python
main.py

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

# Define model Item
class Item(BaseModel):
    name: str

app = FastAPI()

items = {"rock", "paper", "scissors"}


@app.delete("/items")
# Make asynchronous
async def root(item: Item):
    name = item.name
    # Check if name is in items
    if name not in items:
        # Return the status code for not found
        raise HTTPException(status_code=404, detail="Item not found.")
    items.remove(name)
    return {"message": "Item deleted"}
```

## 