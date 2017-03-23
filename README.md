# PHP Panopto API client and libraries.

This is PHP5 implementation of Panopto v4.6 API. It contains client
class for accessing Panopto API public webservices and corresponding libarary
classes for performing the API calls. The client is composer/autoload friendly.

## Features

* All public Panopto v4.6 API webservices supported, i.e.
`AccessManagement`, `Auth`, `RemoteRecorderManagement`,
`SessionManagement`, `UsageReporting`, `UserManagement`.
* Includes *types* classes.
* Follows PSR-4 conventions and autoload friendly.
* Provides client class for simplifying individual webservices access
* Provides script for generating library classes for custom host/version.

## API documentation

Use web or standalone version of documentation published on its offical
website: https://support.panopto.com/articles/Documentation/api-0

## Including library using autoload

`php-panopto-api` is using [Composer](http://getcomposer.org). This is the
most convenient way to start using it in your project.

If you are starting from scratch, download composer first:
```bash
$ curl -s http://getcomposer.org/installer | php
```

Then run the command to require the library:
```bash
$ php composer.phar require lucisgit/php-panopto-api
```

At this stage you should be able to include the library in your project:
```php
<?php

// This file is generated by Composer
require_once 'vendor/autoload.php';
```

## Including library directly

Alternatively, you may clone/download content of `php-panopto-api` repo and
place in in subdirectory of your project. In this case you may include
library directly:

```php
<?php

// php-panopto-api content has been placed in `lib/panopto` sub-directory.
require_once(dirname(__FILE__) . "/lib/panopto/lib/Client.php");
```

## Basic usage

Using client class instance simplifies accessing Panopto webservices and
authentication process. At minimum, you need to know your server hostname
and user credentials. Depending on the type of call you want to make, your user may require specific role. Check API documentation for details.

```php
<?php

// This file is generated by Composer
require_once 'vendor/autoload.php';

// Create client instance for my.panopto.host.
$panoptoclient = new \Panopto\Client('my.panopto.host');
// Create AuthenticationInfo instance with user crdentials.
$panoptoclient->setAuthenticationInfo('username', 'userpassword');

// Get AuthenticationInfo instance for using in calls.
$auth = $panoptoclient->getAuthenticationInfo();

// Get RemoteRecorderManagement webservice instance.
$rrmclient = $panoptoclient->RemoteRecorderManagement();

// Let's list recorders for this simple example. According to API docs we need
// Pagination instance.
$page = 1;
$perpage = 10;
$pagination = new \Panopto\RemoteRecorderManagement\Pagination();
$pagination->setPageNumber($page);
$pagination->setMaxNumberResults($perpage);

// Prepare ListRecorders parameter instance, sort recorders by 'Name'.
$param = new \Panopto\RemoteRecorderManagement\ListRecorders($auth, $pagination, 'Name');

// Perform API call, this return value of ListRecordersResponse type.
$recorderslist = $rrmclient->ListRecorders($param)->getListRecordersResult();

echo "Total recorders count: " . $recorderslist->TotalResultCount;

// Iterate through the list of recorders, each recorder is represented as
// RemoteRecorder type, see API docs for supported properties.
$recorders = $recorderslist->PagedResults->getRemoteRecorder();
foreach($recorders as $recorder) {
    echo "Recorder name: " . $recorder->getName();
    echo "Recorder IP: " . $recorder->getMachineIP();

    // Get devices, each device is represented as RemoteRecorderDevice type,
    // see API docs for supported properties.
    $devices = $recorder->getDevices()->getRemoteRecorderDevice();
    foreach($devices as $device) {
        echo "Device: " . $device->getDeviceType() . ': ' . $device->getName();
    }
}

```

## Building custom library classes.

The repo includes script for generating library classes using
Panopto WSDL interface endpoints. This can be used for making library up to date
with upstream changes or generating classes for the host-specific API version.


