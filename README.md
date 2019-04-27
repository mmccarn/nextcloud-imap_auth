Legacy IMAP Authentication
============================
**Authenticate user login against IMAP using the code from user_external v0.5.0**

Passwords are not stored locally; authentication always happens against
the remote server.

It stores users and their display name in its own database table
`users_external`.
When modifying the `user_backends` configuration, you need to
update the database table's `backend` field, or your users will lose
their configured display name.

If something does not work, check the log file at `nextcloud/data/nextcloud.log`.

IMAP
----
Authenticate Nextcloud users against an IMAP server.
IMAP user and password need to be given for the Nextcloud login.


### Installation
This app is not available in the Nextcloud app store and must be installed manually.  I recommend that you install it into a custom "app" folder to avoid conflicts and file signature checksum errors.  If the app gets used outside of my own servers I will apply to have it added to the app store.

These instructions assume that Nextcloud is installed in /var/www/nextcloud, and the web server is running as 'www-data'.

#### create a folder for local apps
```
cd /var/www/nextcloud
mkdir apps-local
```

#### clone the imap_auth branch into your new apps folder
```
cd /var/www/nextcloud/apps-local
git clone git@github.com:mmccarn/nextcloud-imap_auth.git --branch imap_auth imap_auth
chown -R www-data:www-data imap_auth
```

#### Configure Nextcloud to see the new app folder
Using the instructions in the [admin manual](https://docs.nextcloud.com/server/15/admin_manual/apps_management.html), edit your config.php and insert a section to give us access to 'apps-local':
```
  "apps_paths" => [
      [
              "path"     => OC::$SERVERROOT . "/apps",
              "url"      => "/apps",
              "writable" => false,
      ],
      [
              "path"     => OC::$SERVERROOT . "/apps-local",
              "url"      => "/apps-local",
              "writable" => true,
      ],
  ],
```

#### Enable the new app
Login to your Nexcloud instance as an administrator, browse to 'Apps', locate 'IMAP Authentication' and click 'Enable'


### Configuration
Add the following to your `config.php`:

    'user_backends' => array(
        array(
            'class' => 'OC_IMAP_Auth',
            'arguments' => array(
                '{127.0.0.1:143/imap/readonly}', 'example.com'
            ),
        ),
    ),

This connects to the IMAP server on IP `127.0.0.1`, in readonly mode.
If a domain name (e.g. example.com) is specified, then this makes sure that
only users from this domain will be allowed to login. After successfull
login the domain part will be striped and the rest used as username in
NextCloud. e.g. 'username@example.com' will be 'username' in NextCloud.

Read the [imap_open][0] PHP manual page to learn more about the allowed
parameters.

[0]: http://php.net/imap_open#refsect1-function.imap-open-parameters

### Dependencies
The PHP [IMAP extension][1] has to be activated.

[1]: http://php.net/imap


### Switching from user_external
When switching from user_external, change the user_backend class from 'OC_User_IMAP' to 'OC_IMAP_Auth'.

