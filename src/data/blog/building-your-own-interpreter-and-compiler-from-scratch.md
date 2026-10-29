---
title: building your own interpreter and custom compiler from scratch. 
author: Sunny
pubDatetime: 2026-10-29T04:06:31Z
slug: building-your-own-interpreter-and-compiler-from-scratch
featured: false
draft: false
tags:
  - compiler
description:
  Building a custom compiler is a classic computer science milestone, and modern tools have made it more accessible than ever. You do not have to invent the entire pipeline from scratch unless you want to.
ogImage: https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Screenshot%202026-10-29%20150616.png
---
 Here is the architecture you will be building to transform your custom source code into executable machine code.
--
![Compiler Architecture](https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Screenshot%202026-10-29%20150616.png)

If you want to build your own, you will tackle it in this exact order:

1. **Define Your Grammar:** Keep it tiny at first.
Start by designing the basic rules of your language. Before writing complex loops or classes, define how a simple mathematical expression (like `2 + 3`) or variable assignment should look.


2. **Lexical Analysis (Tokenizer):**
Write a program that reads your raw source text character by character and groups them into meaningful "tokens" (e.g., recognizing that `return` is a keyword and `42` is an integer).


3. **Syntax Analysis (Parser):**
Take those tokens and arrange them into an Abstract Syntax Tree (AST). This enforces your grammar rules, ensuring the tokens make logical sense together in the right order. Tools like **ANTLR** or **Bison** can auto-generate this step for you.


4. **Code Generation:**
Walk through your AST and translate the tree structures into a lower-level language. Instead of writing raw binary machine code yourself, most custom compilers translate their AST into an Intermediate Representation (IR) and hand it off to a backend framework like **LLVM**, which optimizes the code and generates the final binaries for different operating systems.

Building an interpreter from scratch is highly rewarding. Instead of generating machine code (like a compiler), an interpreter reads the code, understands its structure, and executes it directly on the fly.

Here is how to build a miniature Python interpreter that calculates simple math expressions like `"3 + 5"`.

1. **The Lexer (Tokenization):**
The lexer reads your raw text character by character and groups it into a list of categorized "tokens." It ignores whitespace and figures out what is a number and what is an operator.

```python
def lexer(text):
    tokens = []
    for char in text:
        if char.isdigit():
            tokens.append(('NUM', int(char)))
        elif char in '+-*/':
            tokens.append(('OP', char))
    return tokens

# Test it:
tokens = lexer("3 + 5")
print(tokens) 
# Output: [('NUM', 3), ('OP', '+'), ('NUM', 5)]

```


2. **The Parser (Building the AST):**
The parser takes your flat list of tokens and organizes them into an Abstract Syntax Tree (AST) that understands the relationship between the pieces. For this simple language, the operator is the root "parent" node, and the numbers are the "children."

```python
def parser(tokens):
    # We expect a strict pattern: NUM, OP, NUM
    left_node = tokens[0]
    operator = tokens[1]
    right_node = tokens[2]
    
    return {
        'type': 'BinaryOperation',
        'operator': operator[1],
        'left': left_node,
        'right': right_node
    }

ast = parser(tokens)
# The AST is now a nested dictionary representing the operation.

```


3. **The Evaluator (Execution):**
The evaluator walks through the AST and actually performs the actions defined by the tree. It uses recursion to evaluate the children before applying the parent operator.

```python
def evaluate(node):
    # Base case: If it's just a number token, return its value
    if isinstance(node, tuple) and node[0] == 'NUM':
        return node[1]
        
    # Recursive case: It's an operation, so evaluate both sides first
    if node['type'] == 'BinaryOperation':
        left_val = evaluate(node['left'])
        right_val = evaluate(node['right'])
        
        if node['operator'] == '+': return left_val + right_val
        if node['operator'] == '-': return left_val - right_val
        if node['operator'] == '*': return left_val * right_val
        if node['operator'] == '/': return left_val / right_val

result = evaluate(ast)
print(f"Result: {result}") # Output: Result: 8

```


This pipeline—Lexer, Parser, Evaluator—is the foundation of interpreted languages like Python and JavaScript. To scale this up, you would handle multi-digit numbers, order of operations, and eventually add variables and functions.