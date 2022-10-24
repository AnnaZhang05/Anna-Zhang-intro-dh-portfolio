---
layout: page
title: Creating Web APIs with Python and Flask
description: This lesson goes over the basic processes for web API using Python and Flask, which makes data access easier for users. 
---

## Source
[Brandon Walsh, "Creating Web APIs with Python and Flask," Programming Historian (2018), https://doi.org/10.46430/phen0072.](https://programminghistorian.org/en/lessons/creating-apis-with-python-and-flask#setting-up)

## Reflection
API stands for Application Programming Interface, which allows for large sets of data in a computer program to be easily manipulated by another program instead of a human. For Web APIs, it's easier to fetch and deliver data to other interfaces, whilst building a clear URL for user experience. This lesson on Web API uses Python and Flask for the web frameworks. 

I actually came across this lesson after a few trials and many more errors. I initally began with the OCR processing PDF lesson, since I had some field experience using Poppler in practice, thinking implementing coding into it wouldn't be much harder. But due to some technical difficulties including platform differences, the conversion to Linux on my Windows laptop, and other data processing error that came with the lesson, I found that to be unsuccessful. 

However, since the Web API lesson required minimal setup programs (mainly anaconda and VS Code), I was able to tackle it at a better rate. The overall structure of the lesson going from API introductions to hands-on code then explanations of the code was really helpful, since I was able to find answers to some of my error codes in the lesson itself. 

After all, the lesson proved to be just right for a medium difficulty in my opinion, it required some thinking and problem solving without breaking my humanity-student-spirit. The relatively easier start in this lesson also gave me enough motivation to power through and learn more of the actual coding contents in regards to APIs. 

Additional discussion: after learning the basics of fetching data with APIs, I can think of a few ways this tool could be used further in my own research or other people's works. For my own research, the API's ability to fetch and retrieve data from larger collections of information would allow me to access large databases such as the Tisch Special Collections or DCA. Fetching specific excerpts from these large databases, I would be able to critically deliver a small thematic topic to another platform for presentation. For other users, this tool unlocks many possibilities for all the different kinds of data they can pull and deliver to specific crowds with more particular topics. 

## Code

## Setting up Flask and JSON for data fetching

```python
import flask
from flask import request, jsonify
import sqlite3

app = flask.Flask(__name__)
app.config["DEBUG"] = True

def dict_factory(cursor, row):
    d = {}
    for idx, col in enumerate(cursor.description):
        d[col[0]] = row[idx]
    return d

## 'GET' command defines data fetching from "@app.route (base website)"
@app.route('/', methods=['GET'])
def home():
    return '''<h1>Distant Reading Archive</h1>
<p>A prototype API for distant reading of science fiction novels.</p>'''

## 'GET' command defines fetching "all" the books data from the website in "()" after "@app.route." The "return" line returns data of all the books from JSON.
@app.route('/api/v1/resources/books/all', methods=['GET'])
def api_all():
    conn = sqlite3.connect('books.db')
    conn.row_factory = dict_factory
    cur = conn.cursor()
    all_books = cur.execute('SELECT * FROM books;').fetchall()

    return jsonify(all_books)

## Error code and return for invalid searches.
@app.errorhandler(404)
def page_not_found(e):
    return "<h1>404</h1><p>The resource could not be found.</p>", 404

## Sets up query parameters and specifics such as author, year, and ID.
@app.route('/api/v1/resources/books', methods=['GET'])
def api_filter():
    query_parameters = request.args

    id = query_parameters.get('id')
    published = query_parameters.get('published')
    author = query_parameters.get('author')

    query = "SELECT * FROM books WHERE"
    to_filter = []

    if id:
        query += ' id=? AND'
        to_filter.append(id)
    if published:
        query += ' published=? AND'
        to_filter.append(published)
    if author:
        query += ' author=? AND'
        to_filter.append(author)
    if not (id or published or author):
        return page_not_found(404)

    query = query[:-4] + ';'

    conn = sqlite3.connect('books.db')
    conn.row_factory = dict_factory
    cur = conn.cursor()

    results = cur.execute(query, to_filter).fetchall()

    return jsonify(results)
app.run()
```
