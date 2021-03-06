# Bumper
![The web page](https://i.imgur.com/shd4E2B.png)
Remade version of my forum auto bumper with cleaner code and an in-built web server to edit the threads bumped, view statistics, and configure the program remotely, without having to restart.

## Getting Started
### Prerequisites
- An account on [OGUsers](https://ogusers.com)
- A 2Captcha subscription (just incase)
- MongoDB server (if the `--server` option is chosen)

### Installing
```
pip install git+git://github.com/Shoot/Bumper.git
```
Or, clone this repository and type
```
python setup.py install
```

## Usage
### Configuration
All config files follow the following format:
```
{
   "bump_delay": 60.0,
   "post_delay": 60.0,
   "default_message": "Default bumping message",
   "host": "0.0.0.0",
   "port": 80,
   "threads": [
      {
         "name": "Thread name",
         "id": "Thread ID or URL",
         "message": "Custom message for this specific thread"
      }
   ],
}
```
| Field | Required | Information |
|-------|----------|-------------|
| bump_delay | ✓ | The amount of time to wait between bump sessions in minutes |
| post_delay | ✓ | The amount of time to wait between posts when bumping in seconds |
| default_message | ✗ | Default message for threads without a custom message, if this is not specified a built in message will be used instead |
| host | ✗ | IP to run the web server from |
| port | ✗ | The port to run the server on |
| threads | ✓ | An array of threads to be bumped |

Within threads, the following format is used.

| Field | Required | Information |
|-------|----------|-------------|
| name | ✗ | A nickname/alias for the thread |
| id | ✓ | Either the thread ID, or the URL |
| message | ✗ | Custom message for that specific thread |

For now, before installing, go into `/bumper/config.py` and change the value of `SECRET_KEY` to a random, long password.

A full example of a config is available in the `/examples/` folder.

### Running
To sign in with username and password
```
bumper -u <username> -p <password>
```
Or with an `ogusersbbuser` cookie:
```
bumper -c <cookie>
```
The latter option (in my experience) cuts down on the amount of CAPTCHAs encountered.  
Additional options are available below.

| Option | Required | Default | Information |
|--------|----------|---------|------|
| -u | ✓ | None | OGUsers username |
| -p | ✓ | None | OGUsers password |
| -c | ✗ | None | OGUsers cookie (no username/password necessary) |
| -tfa | ✗ | None | Current 2FA code |
| -config | ✗ | config.json | The file name of the config |
| --server | ✗ | False | Run the web server alongside |

All options are available by typing `bumper -h`.

## Website
Using the `--server` parameter when starting the program, a website will be started on `localhost:80` that can be used in order to change the configuration and view statistics as the program is running. There are three different paths of pages that are hosted.
### /admin
Anything to do with the owner/admin of the program.
#### /
View the admin panel of the website.  
Methods: GET  
Parameters: None

### /api
Anything to do with changing configuration for the program from the web. In order to access any parts of this, a valid, logged in session is required.
All methods will return a `200` status code on success and `400` on failure.
#### /config
Change basic configuration for the program.  
Methods: POST  
Parameters:

|Name|Type|Required|Description|
|-|-|-|-|
|bump_delay|Float|✗|Time to wait between bumps|
|post_delay|Float|✗|Time to wait between posts|
|default_message|String|✗|Default message for threads|

#### /thread
Modify threads within the configuration.  
Methods: POST  
Parameters:

|Name|Type|Required|Description|
|-|-|-|-|
|method|String|✓|What method to do for the thread, `edit`, `create`, or `delete`|
|thread|String|✓|The thread user wants to edit|
|name|String|✗|An alias/note for the thread|
|message|String|✗|The default message for that specific thread|

#### /user
Change the information of the admin account.  
Methods: POST  
Parameters:

|Name|Type|Required|Description|
|-|-|-|-|
|old-password|String|✓|Current admin account password|
|new-password|String|✗|New password for the admin account|
|username|String|✗|New username for the account|

#### /last_request
Shows the latest request made by the program as a web page.  
Methods: GET  
Parameters: None

### /auth
Authentication system for the website to prevent just anyone from being able to change the user's configuration.
#### /login
Log in to the website  
Methods: GET/POST  
Parameters:

|Name|Type|Required|Description|
|-|-|-|-|
|username|String|✓|Username to log in as|
|password|String|✓|Password to log in with|

On successful login, user will be redirected to the admin panel (`/admin/`). On failure, they will stay on the page.
#### /logout
Log out of the website  
Methods: GET  
Parameters: None
After logging out the user will be taken to the login page.
#### /register
Signs the user up for an administrator account if there is not already one.  
Methods: GET/POST  
Parameters:

|Name|Type|Required|Description|
|-|-|-|-|
|username|String|✓|Username to use|
|password|String|✓|Password to use|

On successful signup user will be redirected to the admin panel (`/admin/`).

## Todo
- [x] Add functionality to the delete button in the thread section
- [x] Use XHR for the API requests from the admin panel
- [x] Add CAPTCHA support (14/02/2020)
- [ ] Include server specific configuration values (eg for the host/port/secret key/database address)
- [ ] Add proper debug mode
- [ ] Add 'add' function for threads on admin panel
- [ ] Add automatic setting of secret key
- [ ] Store config within database and make config file optional
- [ ] Clean up the `__main__.py` file
- [ ] Add CSRF to the logout endpoint
- [ ] Add more examples

## License
This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](https://github.com/Shoot/Bumper/blob/master/LICENSE) file for details.
