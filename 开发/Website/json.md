##  JavaScript Object Notation
A value can be a string in double quotes, or a number, or true or false or null, or an object or an array. These structures can be nested.  http://www.json.org/index.html

```json
{
    "employees": [
        { 
            "firstName":"Bill" , 
            "lastName":"Gates" 
        },
        { 
            "firstName":"George" , 
            "lastName":"Bush" 
        },
        { 
            "firstName":"Thomas" , 
            "lastName":"Carter" 
        }
    ]
}
```

```js
// JSON.parse -> object
var text0 = '{ "sites" : [' +
	'{ "name":"Google" , "url":"www.google.com" },' +
	'{ "name":"Taobao" , "url":"www.taobao.com" } ]}';
obj = JSON.parse(text0);
alert(obj.sites[1].name);

// JSON.stringify -> string
var object = {
	"sites":[
		{"name":"google","url":"www.google.com"},
		{"name":"jd","url":"www.jd.com"}]
}; 
str = JSON.stringify(object, null, 4)
```

