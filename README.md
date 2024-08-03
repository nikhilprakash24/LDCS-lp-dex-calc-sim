
# Project Documentation: Calculator/Tool for Iterative Calculations

## Overview

This project aims to develop a calculator/tool that runs Python code to perform iterative calculations. The initial version will require users to manually input data, but future versions will integrate API data retrieval and visualization features.

## Objectives

1. **Iterative Calculations**: Implement a system that performs iterative calculations to simulate various scenarios.
2. **User Input**: Design input fields for users to manually enter data in the initial version.
3. **Data Visualization**: Create a space for visualizing data results, with plans to integrate more complex visualizations later.
4. **Modern Development Practices**: Utilize the most modern web development techniques and frameworks.

## Initial Version Features

1. **User Input Fields**: Allow users to input two numbers.
2. **Output Calculation**: Display a single output number based on the input numbers.
3. **Graph Plotting**: Provide a graph area to plot the output number on both the x and y coordinates for simplicity.

## Future Enhancements

1. **API Integration**: Pull data from APIs for automated input.
2. **Advanced Visualization**: Implement more sophisticated data visualization techniques.

## Detailed Plan

### Modern Web Development Stack

To achieve this, we'll use the following modern web development stack:

- **Frontend**: React.js (for dynamic UI)
- **Backend**: FastAPI (for handling Python code execution)
- **Data Visualization**: Plotly (for graphing)
- **Deployment**: Docker (for containerization)

### Frontend (React.js)

- **UI Framework**: React for component-based architecture.
- **Form Handling**: Using React hooks for handling form inputs.
- **Visualization**: Integrating Plotly for dynamic graphing.

### Backend (FastAPI)

- **API Framework**: FastAPI to handle REST API requests.
- **Python Execution**: Running Python code securely and efficiently.
- **Data Processing**: Handling input data and performing calculations.

### Visualization (Plotly)

- **Graphing Library**: Plotly for interactive plots.

### Deployment (Docker)

- **Containerization**: Using Docker to ensure consistent environments.

## Scaffolding

Here's a basic setup:

### Directory Structure

```plaintext
calculator_tool/
├── backend/
│   ├── main.py
│   ├── Dockerfile
│   └── requirements.txt
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   └── Calculator.js
│   │   ├── App.js
│   │   └── index.js
│   ├── Dockerfile
│   └── package.json
├── docker-compose.yml
└── README.md
```

### Backend (FastAPI)

#### `backend/main.py`

```python
from fastapi import FastAPI
from pydantic import BaseModel
from fastapi.middleware.cors import CORSMiddleware
import uvicorn

app = FastAPI()

# CORS Middleware to allow requests from the frontend
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

class CalculationInput(BaseModel):
    number1: float
    number2: float

@app.post("/calculate")
def calculate(input: CalculationInput):
    result = input.number1 + input.number2  # Simple addition for demo
    return {"result": result}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

#### `backend/Dockerfile`

```dockerfile
FROM tiangolo/uvicorn-gunicorn-fastapi:python3.8

COPY ./main.py /app/
COPY ./requirements.txt /app/

RUN pip install --no-cache-dir -r requirements.txt
```

#### `backend/requirements.txt`

```
fastapi
uvicorn
pydantic
```

### Frontend (React.js)

#### `frontend/src/App.js`

```javascript
import React from 'react';
import Calculator from './components/Calculator';

function App() {
  return (
    <div className="App">
      <Calculator />
    </div>
  );
}

export default App;
```

#### `frontend/src/components/Calculator.js`

```javascript
import React, { useState } from 'react';
import Plot from 'react-plotly.js';

const Calculator = () => {
  const [number1, setNumber1] = useState('');
  const [number2, setNumber2] = useState('');
  const [result, setResult] = useState(null);

  const handleCalculate = async () => {
    const response = await fetch('http://localhost:8000/calculate', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ number1: parseFloat(number1), number2: parseFloat(number2) })
    });
    const data = await response.json();
    setResult(data.result);
  };

  return (
    <div>
      <input type="number" value={number1} onChange={(e) => setNumber1(e.target.value)} placeholder="Number 1" />
      <input type="number" value={number2} onChange={(e) => setNumber2(e.target.value)} placeholder="Number 2" />
      <button onClick={handleCalculate}>Calculate</button>
      {result !== null && (
        <div>
          <h3>Result: {result}</h3>
          <Plot
            data={[
              {
                x: [result],
                y: [result],
                type: 'scatter',
                mode: 'markers',
              }
            ]}
            layout={{ width: 320, height: 240, title: 'Result Plot' }}
          />
        </div>
      )}
    </div>
  );
};

export default Calculator;
```

#### `frontend/Dockerfile`

```dockerfile
FROM node:14

WORKDIR /app

COPY package.json /app/
COPY package-lock.json /app/

RUN npm install

COPY . /app/

CMD ["npm", "start"]
```

#### `frontend/package.json`

```json
{
  "name": "calculator-tool",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "dependencies": {
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "react-plotly.js": "^2.5.1",
    "react-scripts": "4.0.3"
  }
}
```

### Docker Compose

#### `docker-compose.yml`

```yaml
version: '3'

services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
```

## Running the Project

1. **Install Docker**: Ensure Docker is installed on your machine.
2. **Build and Run**: Navigate to the project root and run `docker-compose up`.

## Summary

With this scaffolding in place, you have a modern setup for building and expanding your calculator tool. You can start by adding more complex calculations, integrating APIs, and enhancing the visualizations iteratively.

---
