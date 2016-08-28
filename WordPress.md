## Permit upload of any files

Edit wp-config.php and add the following line:
```
define('ALLOW_UNFILTERED_UPLOADS', true);
```

## Setup MultiSite Network

Step 1: 

## Increase time for upload 

https://easyengine.io/tutorials/php/increase-script-execution-time/

## Debug Slow PHP script 

https://easyengine.io/tutorials/php/fpm-slow-log/

## How Add an admin user to the wordpress via MySQL

Exemplo: Insert user demo as admin and ID 4


```sh
INSERT INTO `databasename`.`wp_users` (`ID`, `user_login`, `user_pass`, `user_nicename`, `user_email`, `user_url`, `user_registered`, `user_activation_key`, `user_status`, `display_name`) VALUES ('4', 'demo', MD5('demo'), 'Your Name', 'test@yourdomain.com', 'http://www.test.com/', '2011-06-07 00:00:00', '', '0', 'Your Name');


INSERT INTO `databasename`.`wp_usermeta` (`umeta_id`, `user_id`, `meta_key`, `meta_value`) VALUES (NULL, '4', 'wp_capabilities', 'a:1:{s:13:"administrator";s:1:"1";}');


INSERT INTO `databasename`.`wp_usermeta` (`umeta_id`, `user_id`, `meta_key`, `meta_value`) VALUES (NULL, '4', 'wp_user_level', '10');
```

Source: http://www.wpbeginner.com/wp-tutorials/how-to-add-an-admin-user-to-the-wordpress-database-via-mysql/

