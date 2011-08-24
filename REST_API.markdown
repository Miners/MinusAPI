The REST API
============

Overview
========
At the lowest level, Minus exposes its API with a REST, http-accessible, API. To make it easier for application builders, we will be providing Python, .NET, Ruby, and other API's in the near future.

This file documents the REST API.

The API
=======
We have designed our API to allow you to easily create galleries and upload files programmatically. There are currently 8 API’s published with more to come in the near future. The API is open and no registration is required.

Please subscribe to our http://blog.minus.com for any updates regarding our API and contact us if you have any questions.

CreateGallery
====================

* http://minus.com/api/CreateGallery

Description
----------------

* creates a gallery on the server side and returns the editor and reader id. The editor id combined with the key can be used to upload images into the gallery. 
* On return, the gallery can be immediately viewed on the web site.

Example
-------------
A call returns the below HttpResponse.

	{"editor_id": "eBYwRX", "reader_id": "eBYwRX"}
	
The gallery can be viewed via http://min.us/meBYwRX or http://minus.com/meBYwRX (by adding an **m** before the reader_id).
Galleries can no longer be edited in any browser via their editor_id. As of v2.06, the gallery owner can use the editor_id in the various "edit" APIs (such as UploadItem) to make changes to the gallery, as long as a valid cookie is given along with the request.  The reader_id can also be used instead of the editor_id in any of the Edit APIs.

UploadItem
===================

* http://minus.com/api/UploadItem

Parameters
--------------
* URL should be structured as follows: http://minus.com/api/UploadItem?editor_id=enRlK&key=OK&filename=jcm3U.png
* Body: A standard multipart file POST.
* Should include the **Content-Disposition** header with a value of "**attachment; filename=a.bin**".
* Should include the **Content-Type** header with a value of "**application/octet-stream**".

Result
----------
You can immediately view this picture in any browser by: http://min.us/lcBpkM or http://minus.com/lcBpkM.

	{"id": "cBpkM", "height": 64, "width": 500, "filesize": "1010 bytes"}

Note that you must prefix the id with a /l (if it's an item) or a /m (if it's a gallery) to see access the uploaded item or new gallery.

Example
----------
Note that the editor_id does not have the leading m that is in the page url.

	curl "http://minus.com/api/UploadItem?editor_id=dn48vKBiP3q9&key=OK&filename=min.png" -F "file=@min.png" 

Example Code:
http://minus.com/mvwUFP

SaveGallery
===========
* http://minus.com/api/SaveGallery

Description
----------------
* Use this to update the gallery name or change sort order.
* application/x-www-form-urlencoded
* items=%5B%22A1Q%22%5D&name=test2&key=OK&id=bgZrGCaapOSL2
* items is a json encoded list of the gallery items: {"A1Q", "B1Q"}

Example
----------

    import httplib, urllib, urllib2, json
    params = {"name":"test2","id":"bgZrGCaapOSL2","key":"OK","items":json.dumps(["A1Q"])}
    encoded = urllib.urlencode(params)
    f = urllib2.urlopen('http://minus.com/api/SaveGallery ', encoded)

GetItems
============
* http://minus.com/api/GetItems

Description
----------------

* Use this to retrieve items inside gallery programmatically

Example
----------

http://minus.com/api/GetItems/mvph5BW

	{
		"READ_ONLY_URL_FOR_GALLERY": "vph5BW", 
		"GALLERY_TITLE": "Minus for WP7 Screens", 
		"ITEMS_GALLERY": [
			"http://i.min.us/jlZEMU.PNG", 
			"http://i.min.us/jlVqay.PNG", 
			"http://i.min.us/jlVwyM.PNG", 
			"http://i.min.us/jlZCEM.PNG", 
			"http://i.min.us/jmarjo.PNG", 
			"http://i.min.us/jlVnSq.PNG", 
			"http://i.min.us/jlVQoA.PNG", 
			"http://i.min.us/jlVSwI.PNG", 
			"http://i.min.us/jmavz4.PNG", 
			"http://i.min.us/jmatrw.PNG"
		]
	}

Register
============
* http://minus.com/api/login/register

Description
----------------
* Use this to register a new user.
* POST request type, parameters are **username**, **email**, **password1**, and **password2**.
* **password1** and **password2** must be equal to have a successful registration.
* Will return a JSON dictionary containing a success value, and an optional error variable.
* The email parameter must be included, but can be left blank if the user does not want to sign up with an email.

Possible Return Values
----------------

**Successful Registration**

	{"success": true}
	
**Failed Registration**

	{"success": false}

	{"username": "This username is already taken.", "success": false}

	{"username": "A username is required.", "success": false}

	{"password": "A password is required.", "success": false}

	{"password": "The password does not match.", "success": false}

	{"email": ""This email is invalid.", "success": false}

SignIn
============
* http://minus.com/api/SignIn

Description
----------------

* Use this to sign in a user and get a cookie to use to add galleries to the user's "My Galleries" page.
* POST request type, parameters are **username** and **password1**, with the already registered username and password for the user.
* The reply is a json of a dictionary with a key you should check: "success": True/False. Other values in the dictionary give error information telling the user about invalid username, password, or username-password combination.
* Retain cookie to place subsequent visited or created galleries in the user's "My Galleries" page.

SignOut
============
* http://minus.com/api/SignOut

Description
----------------

* GET request. No params.
* Clears cookie, signs out user.

Timeline APIs
============
* http://minus.com/api/MyGalleries.json
* http://minus.com/api/MyGalleries.json/1
* http://minus.com/api/MyGalleries.json/2
* etc.
* http://minus.com/api/pane/history.json
* http://minus.com/api/pane/mine.json
* http://minus.com/api/pane/feed.json

Description
----------------

* GET request. Optional page number after a forward slash
* Exposes My Galleries page as a convenient json call.
* Total number of available pages are in the "total_pages" entry
* If the edit url is not available, instead of starting with 'm', it will be "Unavailable". If the gallery has been deleted, the value is "Deleted". 
* NEW: The /api/pane/... APIs allow access to the relevant panes from the main website with the same interface as MyGalleries.json. They also accept the same page number after a forward slash
* /pane/history.json is the same as MyGalleries.json

Example
----------

    {
       "total_pages": 1,
       "galleries": [
       
               {
                       "last_visit": "8 seconds ago",
                       "name": "latte32",
                       "item_count": 1,
                       "clicks": 187,
                       "reader_id": "vfPuqV",
                       "editor_id": "Unavailable",
                       "thumbnail_id": "ByEU.jpg",
                       "creator": "Dhleong", 
                       "is_public": "False"
               }
               ,
       
               {
                       "last_visit": "19 seconds ago",
                       "name": "My Piano Performances",
                       "item_count": 7,
                       "clicks": 699,
                       "reader_id": "vwgTH",
                       "editor_id": "Unavailable",
                       "thumbnail_id": "miwrm.jpg",
                       "creator": "Carl", 
                       "is_public": "True"
               }
    }
	


Set Public/Private
================
* http://minus.com/api/set_public/
* http://minus.com/api/set_private/

Description
----------------
* Use to set a gallery to either public or private
* The gallery's **editor_id** should be passed after the last forward slash ('/'), much like with GetItems
* This method does not return anything, but success is checked instead by the HTTP response. If you don't
get 200 OK, it didn't work!

Example
----------

    http://minus.com/api/set_public/EDITOR_ID
    http://minus.com/api/set_private/EDITOR_ID