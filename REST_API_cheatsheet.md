Below is a concise REST API cheat sheet that outlines key design principles along with code examples for Next.js 15 (App Router), Django (with Django REST Framework), FastAPI, and Express.js.

---

## REST API Design Principles

- **Resource-Oriented:**  
  - Use nouns for endpoints (e.g., `/users`, `/items`).
  - Keep URLs intuitive and hierarchical.

- **HTTP Methods:**  
  - **GET:** Retrieve resources.  
  - **POST:** Create a new resource.  
  - **PUT/PATCH:** Update an existing resource.  
  - **DELETE:** Remove a resource.

- **Status Codes:**  
  - **200 OK:** Successful GET/PUT/PATCH/DELETE.  
  - **201 Created:** Successful POST.  
  - **400 Bad Request:** Client-side error.  
  - **404 Not Found:** Resource not found.  
  - **500 Internal Server Error:** Server-side error.

- **Stateless & Consistent:**  
  - Each request should contain all necessary info.
  - Keep responses consistent (e.g., JSON format).

- **Error Handling & Validation:**  
  - Provide meaningful error messages.
  - Validate request payloads and query parameters.

- **Security:**  
  - Use authentication/authorization (e.g., tokens).
  - Protect endpoints with proper CORS settings.

---

## Code Examples

### Next.js 15 (App Router)

*File: `app/api/items/route.js`*

```js
import { NextResponse } from 'next/server';

// GET /api/items - List items
export async function GET() {
  return NextResponse.json({ message: 'List of items' });
}

// POST /api/items - Create an item
export async function POST(request) {
  const data = await request.json();
  // Perform validation and creation logic here
  return NextResponse.json({ message: 'Item created', data }, { status: 201 });
}
```

### Django (with Django REST Framework)

*views.py*

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

class ItemList(APIView):
    # GET /api/items/
    def get(self, request):
        # Logic to retrieve items
        return Response({"message": "List of items"}, status=status.HTTP_200_OK)

    # POST /api/items/
    def post(self, request):
        # Logic to create an item
        return Response({"message": "Item created"}, status=status.HTTP_201_CREATED)
```

*urls.py*

```python
from django.urls import path
from .views import ItemList

urlpatterns = [
    path('api/items/', ItemList.as_view()),
]
```

### FastAPI

*main.py*

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    description: str = None

# In-memory storage for simplicity
items = {}

# GET /items - Retrieve items
@app.get("/items")
async def get_items():
    return items

# POST /items - Create an item
@app.post("/items", status_code=201)
async def create_item(item: Item):
    items[item.name] = item.dict()
    return item
```

### Express.js

*server.js*

```js
const express = require('express');
const app = express();

app.use(express.json());

// GET /api/items - Retrieve items
app.get('/api/items', (req, res) => {
  // Logic to get items
  res.status(200).json({ message: 'List of items' });
});

// POST /api/items - Create an item
app.post('/api/items', (req, res) => {
  // Logic to create an item using req.body
  res.status(201).json({ message: 'Item created', data: req.body });
});

const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

## Quick Checklist

- **Endpoint Naming:** Use clear, plural nouns (e.g., `/items`).
- **HTTP Methods:**  
  - GET for fetching,  
  - POST for creating,  
  - PUT/PATCH for updating,  
  - DELETE for removal.
- **Response Format:** Use JSON.
- **Status Codes:** Match actions to the proper HTTP codes.
- **Validation:** Always validate inputs.
- **Security:** Implement authentication, authorization, and proper CORS.

This cheat sheet should serve as a quick reference whenever you build or maintain a REST API using these technologies. Happy coding!
