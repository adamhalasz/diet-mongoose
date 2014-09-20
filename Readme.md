# **diet-mongoose**
A [diet][1] plugin for the official mongoose module:

- Mongoose Github: [https://github.com/LearnBoost/mongoose][2]
- Mongoose Website: [http://mongoosejs.com/][3]

## **Dependencies**
Make sure you installed [diet][4], [mongoose][6] and [mongodb][5] as well. 

For [diet][4] and [mongoose][3]:
```
npm install diet
npm install mongoose
```
For mongodb follow the instructions from the official manual: http://docs.mongodb.org/manual/installation/


## **Install**
```
npm install diet-mongoose
```

## **Example Usage**
Mongoose is used in a different way as a plugin because of it's persistent nature. We are actually not making much difference by using it as a plugin. 

For now as a plugin it makes the setup slightly more simple. It also makes visible on the plugin list when you launch your app.

**index.js**
```js
// Require Diet
var server = require('diet')

// Require Mongoose
require('mongoose')

// Create App
var app = module.app = new server()

// Configure Domain
app.domain('http://localhost:8000/')

// Plugin Mongoose
app.plugin('diet-mongoose', { database: 'yourDatabase' })

// Start HTTP Server
app.start()

// Require Schema
require('./blog.js')

// Require Router
require('./router.js')

// Export App
module.exports = app
```
**blog.js**
```js
// Require Mongoose
var mongoose = require('mongoose')

// Create a Schema
var blogSchema = new mongoose.Schema({
  title:  String,
  author: String,
  body:   String,
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },
  hidden: Boolean,
  meta: {
    votes: Number,
    favs:  Number
  }
})

// Export Model
module.exports = mongoose.model('Blog', blogSchema)
```

**router.js**
```js
// Require App
var app = module.parent.app

// Require Blog Model
var Blog = require('./blog.js')

// Show All Posts
app.get('/', function($){
    Blog.find(function(error, posts){
        if(error) throw error
        $.data.posts = posts
        $.json()
    })
})

// Find Post by ID
app.get('/post/:id', function($){
    Blog.find({ _id: $.params.id }, function(error, post){
        if(error) throw error
        $.data.post = post || false
        $.json()
    })
})

// Save Post
app.post('/save', function($){
    Blog.save($.request.body, function(error, post){
        if(error) throw error
        $.redirect('home')
    })
})

// Delete Post by ID
app.get('/delete/:id', function($){
    Blog.remove({ _id: $.params.id }, function(error){
        if(error) throw error
        $.redirect('home')
    })
})
```

## **Config**
These are the default config options. All of them are optional, but you probably want to change the `database` at least: 
```js
app.plugin('diet-mongoose', {
	database : 'test',
	port	 : '27017',
	host	 : 'localhost',
	protocol : 'mongodb'
})
```

# **License**
(The MIT License)

Copyright (c) 2014 Halász Ádám <mail@adamhalasz.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.


  [1]: http://dietjs.com/
  [2]: https://github.com/LearnBoost/mongoose/
  [3]: http://mongoosejs.com/
  [4]: http://dietjs.com/
  [5]: http://mongodb.org/
  [6]: http://mongoosejs.com