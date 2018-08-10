# update-Confluence-page-use-RESTAPI
This project is about using RESTAPI to auto create and update pages of Confluence. It also involves using Jenkins to trigger the code and how to check the console output to debug.
The programming code language is Python 2.6.6. 

## 1. Confluence RESTAPI
<p> Confluence's REST APIs provide access to resources (data entities) via URI paths. To use a REST API, your application will make an HTTP request and parse the response. By default, the response format is XML. If you wish, you can request JSON instead of XML. Your methods will be the standard HTTP methods like GET, PUT, POST and DELETE.
Because the REST API is based on open standards, you can use any web development language to access the API.</p>
<p>   For my project I used <b>JSON and Python</b>.</p>
<p> Useful link for Confluence RESTAPI: </p>
https://developer.atlassian.com/server/confluence/confluence-rest-api-examples/
https://docs.atlassian.com/DAC/rest/confluence/5.2.html

### Request
There are two way to request a page:
<li>Use the page id</li> 

```python
page_id = xxx
auth = {"user":"xxx", "password":"xxx"}
page = os.popen('curl -u {0}:{1} -X GET "https://confluence.com/confluence/rest/api/content/{2}?expand=version,body.storage"'.format(auth['user'], auth['password'], page_id)).read()
```
https://confluence.atlassian.com/confkb/how-to-get-confluence-page-id-648380445.html

<li>Use the title and spaceKey</li> 

```python
confluence_year = XXXX
confluence_month = XXXX
auth = {"user":"xxx", "password":"xxx"}
page = json.loads(os.popen('curl -u {0}:{1} -X GET "https://confluence.com/confluence/rest/api/content?title={2}%20-%20{3}&spaceKey=xxxx"'.format(auth['user'], auth['password'], confluence_page_month, confluence_page_year)).read())
```
### Post
```python
data = {
    		"body": {
        		"storage": {
            			"representation": "storage",
            			"value": "<p> Reports list </p>",
        		}
    		},
    		"ancestors": [{"id":id}],
    		"space": {"key":"xxxx"},
    		"title": "{} - {}".format(confluence_page_month, confluence_page_year),
    		"type": "page"
	}
auth = {"user":"xxx", "password":"xxx"}
cmd = "curl -u {0}:{1} -X POST -H 'Content-Type: application/json' -d '{2}' https://confluence.com/confluence/rest/api/content/".format(auth['user'], auth['password'], data)
assert not os.system(cmd)
```
### PUT
```python
data = {
    "body": {
        "storage": {
            "representation": "storage",
            "value": <p> Reports list </p>,
        }
    },
    "title": "{} - {}".format(confluence_page_month, confluence_page_year),
    "type": "page",
    "version": {
	"number":page['version']['number']+1,
    }
}
update_data = json.dumps(data)
cmd = "curl -u {0}:{1} -X PUT -H 'Content-Type: application/json' -d '{2}' https://confluence. .com/confluence/rest/api/content/{3}".format(auth['user'], auth['password'], update_data,page_id)
res = os.system(cmd)
assert not res
```
### Important notes:
#### a. XHTML vs HTML
Confluence accept XHTML not HTML code when doing the POST/PUT method, so make sure to have clean HTML code When using POST/PUT method. eg. XHTML must Always Be Closed. Data in \<pre\> \<\/pre\> tag might not accepted by Confluence.
<li> Useful link for Confluence RESTAPI: </li>
Difference between XHTML and HTML: https://www.w3schools.com/html/html_xhtml.asp
#### b. Request or curl
Since the code is trigger in Jenkins with linux environment, the version of python is old and not support <b>request module</b>. I just use curl.


## 2. JSON code format
<p>you can use JSON formatter to beautify and easier to read</p>
https://jsonformatter.curiousconcept.com/
<p>For the properties of JSON code requested and how to format, check the documentation:</p>
https://developer.atlassian.com/server/confluence/confluence-rest-api-examples/

## 3. Python tricks
#### JSON to python encode decode
#### python formating
#### slicing
#### Parser
#### tools to use
Jupyter
## 4. Python version problem
## 5. Large data set
## 6. Git command tips
https://gist.github.com/nepsilon/156387acf9e1e72d48fa35c4fabef0b4

combine git commits to one commit


## 7. Shell scripts
## 8. Linux command
### .bashrc
## 9. Use Jenkins console output to debug
