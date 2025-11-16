## Unit tests vs. system tests

Classify the characteristics of unit tests and system tests.

| Unit Tests | System Tests |
| ------- | ------ |
| Use `pytest` | Use `pytest` with `TestClient` |
| Validate code block | Validae system function |
| Require isolated environment | Require runing application |

## System test

You've built your FastAPI application and added unit tests to verify code functionality. Writing a system test for an API endpoint will ensure that the endpoint works on the running application.

We can't run the FastAPI server directly with "Run this file" - see the instructions for how to run the server and test your code from the terminal.

### Instructions

1. Review the GET endpoint defined in `main.py`.
2. Complete the following system test in `system_test.py`.
3. In the terminal, run `pytest`.

```python
main.py

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional

# define model Item
class Item(BaseModel):
    name: str
    quantity: Optional[int] = 0

app = FastAPI()

items = {"scissors": Item(name="scissors", quantity=100)}


@app.get("/items")
def read(name: str):
    if name not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return items[name]
```
```python
system_test.py

# Import TestClient
from fastapi.testclient import TestClient
from main import app

# Create test client with application context
client = TestClient(app)

def test_main():
    response = client.get("/items?name=scissors")
    assert response.status_code == 200
    assert response.json() == {"name": "scissors",
                               "quantity": 100}
```

## HTTP operations & CRUD steps

Match each HTTP operation with its corresponding CRUD step.

| Create | Read | Update |
| ------ | ------ | ------ |
| POST | GET | PUT |

## DELETE operation response

What data should a DELETE endpoint return with a successful response?

It should return an empty response.

## Complete JSON CRUD API

You've been asked to build a JSON CRUD API to manage item names and quantities. To test your API you need to create an item, read it, update it, delete, and verify it's been deleted.

We can't run the FastAPI server directly with "Run this file" - see the instructions for how to run the server and test your code from the terminal.

### Instructions

1. Fill in the missing HTTP operations and status codes in `main.py`.
2. Run the live server from the terminal: `fastapi dev main.py`
3. Open a new terminal (top-right of terminal) and test your code with the following five commands:

```
curl -X POST \
  -H 'Content-Type: application/json' \
  -d '{"name": "rock"}' \
  http://localhost:8000/items

curl http://localhost:8000/items?name=rock

curl -X PUT \
  -H 'Content-Type: application/json' \
  -d '{"name": "rock", "quantity": 100}' \
  http://localhost:8000/items

curl -X DELETE \
  -H 'Content-Type: application/json' \
  -d '{"name": "rock"}' \
  http://localhost:8000/items

curl http://localhost:8000/items?name=rock
```
4. Verify that the first four commands return `200 OK`, and the final command returns `404 Not Found`

```python
main.py

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional

# define model Item
class Item(BaseModel):
    name: str
    quantity: Optional[int] = 0

app = FastAPI()

items = {}


@app.post("/items")
def create(item: Item):
    name = item.name
    if name in items:
        raise HTTPException(status_code=409, detail="Item exists")
    items[name] = item
    return {"message": f"Added {name} to items."}
  
@app.get("/items")
def read(name: str):
    if name not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return items[name]  
  
@app.put("/items")
def update(item: Item):
    name = item.name
    if name not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    items[name] = item
    return {"message": f"Updated {name}."}
  
@app.delete("/items")
def delete(item: Item):
    name = item.name
    if name not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    del items[name]
    return {"message": f"Deleted {name}."}
```

## System tests vs. functional tests

Classify the characteristics as system or functional tests.

| System Tests | Functional Tests |
| ------- | ------ |
| Use `pytest` with `TestClient` | Use `requests` in a script |
| Test one endpoint alone | Test multiple endpoints together |
| Validate system function | Validate application overall |

## Functional test

You've built your FastAPI application and added system tests to verify the functionality of each endpoint. Building a functional test for a core API workflow will ensure that the endpoints work together for the full life cycle of your data.

We can't run the FastAPI server directly with "Run this file" - see the instructions for how to run the server and test your code from the terminal.

### Instructions

1. Review the CRUD API defined in `main.py`.
2. Complete the following functional test workflow in `functional_test.py`.
3. Run the live server from the terminal: `fastapi dev main.py`.
4. Open a new terminal (top-right of terminal) with `functional_test.py` open and click "Run this file" to test your code.

```python
main.py

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional

# define model Item
class Item(BaseModel):
    name: str
    quantity: Optional[int] = 0

app = FastAPI()

items = {}


@app.post("/items")
def create(item: Item):
    name = item.name
    if name in items:
        raise HTTPException(status_code=409, detail="Item exists")
    items[name] = item
    return {"message": f"Added {name} to items."}
  
@app.get("/items")
def read(name: str):
    if name not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return items[name]  
  
@app.put("/items")
def update(item: Item):
    name = item.name
    if name not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    items[name] = item
    return {"message": f"Updated {name}."}
  
@app.delete("/items")
def delete(item: Item):
    name = item.name
    if name not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    del items[name]
    return {"message": f"Deleted {name}."}
```
```python
functional_test.py

import requests

ENDPOINT = "http://localhost:8000/items"

# Create item "rock" without providing quantity
r = requests.post(ENDPOINT, json={"name": "rock"})
assert r.status_code == 200
assert r.json()["message"] == "Added rock to items."

# Verify that item "rock" has quantity 0
r = requests.get(ENDPOINT + "?quantity=0")
assert r.status_code == 200
assert r.json()["quantity"] == 0

# Update item "rock" with quantity 100
r = requests.put(ENDPOINT, json={"name": "rock", "quantity": 100})
assert r.status_code == 200
assert r.json()["message"] == "Updated rock."

# Verify that item "rock" has quantity 100
r = requests.get(ENDPOINT + "?name=rock")
assert r.status_code == 200
assert r.json()["quantity"] == 100

# Delete item "rock"
r = requests.delete(ENDPOINT, json={"name": "rock"})
assert r.status_code == 200
assert r.json()["message"] == "Deleted rock."

# Verify that item "rock" does not exist
r = requests.get(ENDPOINT + "?name=rock")
assert r.status_code == 404

print("Test complete.")
```