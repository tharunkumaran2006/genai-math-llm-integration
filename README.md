## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python function for calculating the basic arithmetic operations, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

### PROBLEM STATEMENT: 
Design and implement a Python function to calculate the basic arithmetic operations and integrate it with an LLM-powered chat system using function-calling. 

### DESIGN STEPS:

#### STEP 1: Load environment variables and set up the OpenAI API key for authentication.

#### STEP 2: Implement calculator(operation,num1,num2), which computes and returns the volume in JSON format.

#### STEP 3: Create a function definition in JSON format (functions list) to enable OpenAI's function-calling capability.

#### STEP 4: Send a user’s request to the OpenAI chat model (ChatCompletion.create) along with function metadata.

#### STEP 5:  If the model invokes calculator, extract arguments, compute the result, append it to the conversation, and send a final request to get the AI's response.

### PROGRAM:
```python
import os
import openai

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
openai.api_key = os.environ['OPENAI_API_KEY']
import json

def calculator(operation, num1, num2):
    """Perform basic arithmetic operations"""
    result = None

    if operation == "add":
        result = num1 + num2
    elif operation == "subtract":
        result = num1 - num2
    elif operation == "multiply":
        result = num1 * num2
    elif operation == "divide":
        result = num1 / num2 if num2 != 0 else "Error: Division by zero"
    else:
        result = "Error: Invalid operation"

    calc_info = {
        "operation": operation,
        "number1": num1,
        "number2": num2,
        "result": result,
    }
    return json.dumps(calc_info)
# define a function
functions = [
    {
        "name": "calculator",
        "description": "Perform basic arithmetic operations on two numbers",
        "parameters": {
            "type": "object",
            "properties": {
                "operation": {
                    "type": "string",
                    "enum": ["add", "subtract", "multiply", "divide"],
                    "description": "The operation to perform",
                },
                "num1": {
                    "type": "number",
                    "description": "The first number",
                },
                "num2": {
                    "type": "number",
                    "description": "The second number",
                },
            },
            "required": ["operation", "num1", "num2"],
        },
    }
]
messages = [
    {
        "role": "user",
        "content": "Calculate 729 divided by 9"
    }
]
import openai

# Call the ChatCompletion endpoint
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=messages,
    functions=functions,
    function_call="auto"  # Let the model decide when to call the function
)

print(response)

response_message = response["choices"][0]["message"]
response_message

response_message["content"]
response_message["function_call"]

json.loads(response_message["function_call"]["arguments"])

args = json.loads(response_message["function_call"]["arguments"])
calculator(**args)
```

### OUTPUT:
<img width="1118" height="270" alt="Screenshot 2025-09-19 102953" src="https://github.com/user-attachments/assets/c502a940-2c8e-4168-a383-79cd0cdcf682" />

### RESULT: 
The code enables LLM-driven Calculator performing basic arithmetic operations via function calling.
