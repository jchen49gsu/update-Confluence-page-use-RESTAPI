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
Useful link for Confluence RESTAPI: 
<p>Difference between XHTML and HTML: https://www.w3schools.com/html/html_xhtml.asp </p>


#### b. Request or curl
Since the code is trigger in Jenkins with linux environment, the version of python is old and not support <b>request module</b>. I just use curl.


## 2. JSON code format
<p>you can use JSON formatter to beautify and easier to read</p>
https://jsonformatter.curiousconcept.com/
<p>For the properties of JSON code requested and how to format, check the documentation:</p>
https://developer.atlassian.com/server/confluence/confluence-rest-api-examples/

## 3. Python tricks
#### a. JSON to python encode decode
<p><b>Decoding JSON:</b></p>
<p>json.loads</p>
<p><b>Encoding JSON:</b></p>
<p>json.dumps</p>

https://docs.python.org/2/library/json.html



#### b. python formating (%s vs .format)
```python
'%s %s' % ('one', 'two')
'{} {}'.format('one', 'two')
```
https://pyformat.info/

#### c. Slicing
```python
idx=curr_page.index('<p> Reports list </p>')
new_list = '<p> new list </p>'
new_curr_page = curr_page[:idx] + new_list + curr_page[idx:]
```
https://www.pythoncentral.io/cutting-and-slicing-strings-in-python/

#### d. Parser for command-line options, arguments and sub-commands
```python
import argparse
parser = argparse.ArgumentParser()

parser.add_argument('-htmlDir')
parser.add_argument('-buildNumber')

args = parser.parse_args()
with open(args.htmlDir + '/test.html', 'r') as fh:
    report= fh.read()
```
https://docs.python.org/2/library/argparse.html

#### e. tools to use

Jupyter to run python code
http://jupyter.org/install

## 4. Python version problem
Notes there can be version problem, you can check official documents to use correct function.
https://docs.python.org/2/index.html


## 5. Large data set
When I posted large data set, the confluence couldn't post it. 
Following links might help.


http://www.diveintopython3.net/http-web-services.html

gzip in java
http://docs.jboss.org/resteasy/docs/2.2.1.GA/userguide/html/gzip.html

curl to send the data
http://yasassriratnayake.blogspot.com/2015/08/sending-gzip-request-using-curl.html
https://stackoverflow.com/questions/21326397/curl-get-request-with-json-parameter



stream example ******
https://pymotw.com/2/gzip/

curl -v -i http://localhost/mymodule -H'Content-Encoding: gzip' --data-binary @body.gz
https://stackoverflow.com/questions/44307771/curl-command-a-gzipped-post-body-to-an-apache-server

python library
https://docs.python.org/release/2.6.6/library/zlib.html?highlight=compress

https://docs.python.org/2.7/library/gzip.html#gzip.GzipFile


read part of file
https://stackoverflow.com/questions/12761371/read-head-tail-of-gzip-file-without-reading-it-into-memory

no attribute problem
https://stackoverflow.com/questions/32884277/how-can-i-avoid-zipfile-instance-has-no-attribute-exit-when-extracting


convert curl to python

https://curl.trillworks.com/

with open syntax
https://www.pythonforbeginners.com/files/reading-and-writing-files-in-python

Bottle/Python - “IOError: [Errno 9] read() on write-only GzipFile object” when trying to decompress request body
https://stackoverflow.com/questions/32434352/bottle-python-ioerror-errno-9-read-on-write-only-gzipfile-object-when-t


https://www.programcreek.com/python/example/252/gzip.GzipFile

https://www.programcreek.com/python/example/2166/pycurl.POST
https://curl.haxx.se/libcurl/c/curl_mime_init.html
https://curl.haxx.se/mail/lib-2009-12/0184.html

confluence api example
https://cbsnorthstar.atlassian.net/wiki/spaces/ORDERENTRY/pages/80871619/Web+Ordering+REST+API
https://developer.atlassian.com/server/confluence/confluence-rest-api-examples/

pagiation
https://developer.atlassian.com/server/confluence/pagination-in-the-rest-api/
https://developer.atlassian.com/server/confluence/content-properties-in-the-rest-api/

https://idratherbewriting.com/learnapidoc/docapis_understand_curl.html


header, Accept-Encoding: gzip
https://stackoverflow.com/questions/43209924/rest-api-use-the-accept-application-json-http-header
https://developers.google.com/blogger/docs/3.0/performance
https://github.com/mozilla/MozStumbler/issues/406


Python StringIO replacement that works with bytes instead of strings?

https://stackoverflow.com/questions/6479100/python-stringio-replacement-that-works-with-bytes-instead-of-strings
https://stackoverflow.com/questions/32075135/python-3-in-memory-zipfile-error-string-argument-expected-got-bytes/32075279

https://docs.python.org/2.6/library/json.html



## 6. Git command tips
https://gist.github.com/nepsilon/156387acf9e1e72d48fa35c4fabef0b4

combine git commits to one commit

### Clone repo while in workspace
git clone --recursive ssh://git@xxxxxxxxxxxxxxxxx.git myrepo

### Create a branch to do work that you plan to check-in. 
git checkout -b mybranch

### Check your branch
git branch

### switch to other branch
git checkout branchname

### Check the remote branch
git branch -vv

### Change the remote branch name
git branch -u remoteBranchName

### add changes
git add -u

### add specific changes
git cherry-pick commitNumber

### abort the cherry-pick change
git cherry-pick --abort

### Make some changes, commit them 
git commit -a -m “message” 

### Change the code but bot change the commit message
git commit --amend --no-edit

### Change the commit information
git commit --amend

### combine multiple commits to one commit (in local)
git rebase -i branchName
•https://robots.thoughtbot.com/git-interactive-rebase-squash-amend-rewriting-history
•https://git-scm.com/book/en/v2/Git-Branching-Rebasing
p: pick, pick the one to remain
s: squash, meld into previous one

### update to most recent from the remote
git pull --rebase

### Push to remote branch
git push --set-upstream remoteRepo         or
git push origin remoteRepo

### check status
git status

###show log history
git log

### show detail log
git show commitNumber

### show all the command you use
history

## 7. Shell scripts
## 8. Linux command
### .bashrc
## 9. Use Jenkins console output to debug
