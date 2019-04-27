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


### Configuration
The parameters are `host, port, sslmode, domain`.
Possible values for sslmode are `ssl` or `tls`.
Add the following to your `config.php`:

    'user_backends' => array(
        array(
            'class' => 'OC_IMAP_Auth',
            'arguments' => array(
                '127.0.0.1', 993, 'ssl', 'example.com'
            ),
        ),
    ),

This connects to the IMAP server on IP `127.0.0.1`.
The default port is 143. However, note that parameter order matters and if
you want to restrict the domain (4th parameter), you need to also specify
the port (2nd parameter) and sslmode (3rd parameter; set to `null` for
insecure connection).
If a domain name (e.g. example.com) is specified, then this makes sure that
only users from this domain will be allowed to login. After successfull
login the domain part will be striped and the rest used as username in
Nextcloud. e.g. 'username@example.com' will be 'username' in Nextcloud.

Read the [imap_open][0] PHP manual page to learn more about the allowed
parameters.

[0]: http://php.net/imap_open#refsect1-function.imap-open-parameters


### Dependencies
The PHP [IMAP extension][1] has to be activated.

[1]: http://php.net/imap


### Switching from user_external
When switching from user_external, change the user_backend class from 'OC_User_IMAP' to 'OC_IMAP_Auth'.

