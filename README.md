# MediaWiki OAuth2 Client
MediaWiki implementation of the PHP League's [OAuth2 Client](https://github.com/thephpleague/oauth2-client), to allow MediaWiki to act as a client to any OAuth2 server.
Based on the awesome repository from [Schine GmbH](https://www.star-made.org/).

Requires MediaWiki 1.25+.

## Installation

Clone this repo into the extension directory. In the cloned directory, run 'git submodule update --init' to initialize the local configuration file and fetch all data from the OAuth2 client library.

Finally, run [composer](https://getcomposer.org/) in /vendors/oauth2-client to install the library dependency.

```
git clone https://github.com/provinzio/MW-OAuth2Client.git
cd MW-OAuth2Client
git submodule update --init
cd vendors/oauth2-client
composer install
```

## Usage

Add the following line to your LocalSettings.php file.

```php
wfLoadExtension( 'MW-OAuth2Client' );
```

Required settings to be added to LocalSettings.php

```php
$wgOAuth2Client['client']['id']     = ''; // The client ID assigned to you by the provider
$wgOAuth2Client['client']['secret'] = ''; // The client secret assigned to you by the provider

$wgOAuth2Client['configuration']['authorize_endpoint']     = ''; // Authorization URL
$wgOAuth2Client['configuration']['access_token_endpoint']  = ''; // Token URL
$wgOAuth2Client['configuration']['api_endpoint']           = ''; // URL to fetch user JSON
$wgOAuth2Client['configuration']['redirect_uri']           = ''; // URL for OAuth2 server to redirect to

$wgOAuth2Client['configuration']['username'] = 'username'; // JSON path to username
$wgOAuth2Client['configuration']['email'] = 'email'; // JSON path to email
```

The JSON path should be set to point to the appropriate attributes in the JSON.

If the properties you want from your JSON object are nested, you can use periods.

For example, if user JSON is

```json
{
    "user": {
        "username": "my username",
        "email": "my email"
    }
}
```

Then your JSON path configuration should be these

```php
$wgOAuth2Client['configuration']['username'] = 'user.username'; // JSON path to username
$wgOAuth2Client['configuration']['email'] = 'user.email'; // JSON path to email
```

You can see [Json Helper Test case](./tests/phpunit/JsonHelperTest.php) for more.

The **user's real name** will be set to the username by default.
Depending on what you get from your backend, you may also want to configure:
```php
$wgOAuth2Client['configuration']['real_name'] = 'realname'; // JSON path to real name
```
or:
```php
$wgOAuth2Client['configuration']['first_name'] = 'first_name'; // JSON path to first name
$wgOAuth2Client['configuration']['last_name'] = 'last_name'; // JSON path to last name
```

The **Redirect URI** for your wiki should looking like this.
Please make sure, that mediawiki localizes `Special`, so you have to adapt it to your chosen language.

```
http://your.wiki.domain/path/to/wiki/Special:OAuth2Client/callback
```

Optional further configuration

```php
$wgOAuth2Client['configuration']['http_bearer_token'] = 'Bearer'; // Token to use in HTTP Authentication
$wgOAuth2Client['configuration']['query_parameter_token'] = 'auth_token'; // query parameter to use
$wgOAuth2Client['configuration']['scopes'] = 'read_citizen_info'; //Permissions

$wgOAuth2Client['configuration']['service_name'] = 'Citizen Registry'; // the name of your service
$wgOAuth2Client['configuration']['service_login_link_text'] = 'Login with StarMade'; // the text of the login link

$wgOAuth2Client['configuration']['group'] = 'group'; // JSON path to group, if set group_mapping is also required
$wgOAuth2Client['configuration']['groups'] = 'groups'; // JSON path to array of groups, if set group_mapping is also required
$wgOAuth2Client['configuration']['group_id'] = 'group_id'; // JSON path to group_id, if set group_mapping is also required
$wgOAuth2Client['configuration']['group_mapping'] = [
	1 => 'sysop',  // Give users with group 1 the mediawiki group 'sysop'
];
```

Optional Authorization Callback

Provide a callback and error message in the configuration that evaluates a conditional based upon the result of some business logic provided by the authorization endpoint response.

```php
$wgOAuth2Client['configuration']['authz_callback'] = function($response) {
  if ($response['property']) {
    return true;
  } else {
    return false;
  }
}; // return true or false based on something from the authorization response
$wgOAuth2Client['configuration']['authz_failure_message'] // text of error message
```



### Popup Window
To use a popup window to login to the external OAuth2 server, copy the JS from modal.js to the [MediaWiki:Common.js](https://www.mediawiki.org/wiki/Manual:Interface/JavaScript) page on your wiki.

### Extension page
https://www.mediawiki.org/wiki/Extension:OAuth2_Client

## License
LGPL (GNU Lesser General Public License) http://www.gnu.org/licenses/lgpl.html
