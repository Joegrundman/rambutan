---
layout: post
section-type: post
title: Native Templating With Node
category: tech
tags: [ 'node', 'templating' ]
---

If you have a lot of templating to do, you would like to use a templating library like Handlebars or Swig.

But what if you have only a few pages that need to be templated? Do you need to install the library and configure your express app for just one or two pages?

The new node features of **template string interpolation** and **multiline strings** make creating your own templates pretty easy without need for _handlebars_

let's say we have templating that calls for variables. using swig it would look like this

{% raw %}
```
<div class="container">
   <h3 id="pollTitle">{{title}}</h3> by <span id="pollAuthor">{{author}}</span>
</div>
```
{% endraw %}
but this can be done just as easily with node template interpolation.

```
`
<div class="container">
   <h3 id="pollTitle">${title}</h3> by <span id="pollAuthor">${author}</span>
</div>
`
```

wrap it as a function,and we have a reusable component

```
function getTitle(title, author){
   return (`
<div class="container">
   <h3 id="pollTitle">${title}</h3> by <span id="pollAuthor">${author}</span>
</div>   
   `)
}
```

how about for loops? in swig

{% raw %}
```
<div class="fieldContainer">
   {% for field in fields %}
      <div>
         <h4>{{field.name }}: <span>{{field.votes}}</span></h4>
      </div>
   {% endfor %}
</div>
```
{% endraw %}
sure, we can do this too using template string interpolation

```
`
<div class="fieldContainer">
   ${fields.map(field => (`      
      <div>
         <h4>${field.name }: <span>${field.votes}</span></h4>
      </div>`).join('')}
</div>
`
```

one line fewer!

the loop can also be extracted into a separate part of the template

```
function getFields(data){
   var fields = data.fields.map(field => (`      
         <div>
            <h4>${field.name }: <span>${field.votes}</span></h4>
         </div>`))

   return(`
      <div class="fieldContainer">
         ${fields}
      </div>
   `)
}
```

here is a complete template page made using native node, mixed with logic in a way that is familiar to users of react and angular

*/templates/poll_template.js*

```

module.exports = function(poll){
	
   function getField(field) {
      return (`
   	<div>
         <h4>${field.name }: <span id="votes_${field._id}">${field.votes}</span></h4>
         <button id="${poll.title}/${field.id}" class="btn-vote" onclick="upVote(this)">Vote</button>
      </div>
   `)
   }

   return (`
<!DOCTYPE html>

<html>

	<head>
		<title>demonstration of native node template</title>
		<link href="/public/css/main.css" rel="stylesheet" type="text/css">
	</head>

	<body>

		<div class="container">
         <h1>Individual Poll Page</h1>
		</div>		
      <div class="container">
         <h3 id="pollTitle">${poll.title}</h3> by <span id="pollAuthor">${poll.author}</span>
      </div>
		<div class="fieldContainer">
         ${poll.fields.map(f => getField(f)}
		</div>
      <br>
		<button type="button" id="addField_${poll.title}" class="btn btn-info" onclick="addField(this)">Add Field</button>
		<br>
		<br>
		<a href="/"><button class="btn btn-info">Go Back</button></a>

		<!--  note these resources go back one directory otherwise looks from /poll/-->
		<script type="text/javascript" src="../common/ajax-functions.js"></script>
		<script type="text/javascript" src="../controllers/singlePollController.client.js"></script>
	</body>

</html>
`)
}


```
then in your express app you call the template and send it to the client

```
var pollTpl = require('../templates/poll_template')(data)
res.send(pollTpl)
```


**conclusion**

with the new *template string interpolation* feature, templates can  
 be quite easily made without external libraries. I wouldn't want   
 to say there is no use for the template libraries. They are long established  
 and mature, but it is worth asking yourself if you need it.