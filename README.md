LdapBundle
==========

LdapBundle provides a Ldap authentication system without the `apache mod_ldap`. It uses `php-ldap` extension with a form to authenticate the users. LdapBundle also can be used for the authorization. It retrieves the  Ldap users' roles.

Contact
-------
You can try to contact me on freenode irc ; channel #symfony-fr ; pseudo : aways

Install
-------
1. Download LdapBundle
2. Configure the Autoloader
3. Enable the Bundle
4. Configure LdapBundle security.yml
6. Import LdapBundle security.yml
7. Import LdapBundle routing
8. Implement Logout

### Get the Bundle

#### Method a) Using the `deps` file

Add the following lines to your  `deps` file and then run `php bin/vendors
install`:

```
[LdapBundle]
    git=https://github.com/BorisMorel/LdapBundle.git
    target=bundles/IMAG/LdapBundle
```

#### Method b) Using submodules

Run the following commands to bring in the needed libraries as submodules.

``` bash
$ git clone git://github.com/BorisMorel/LdapBundle.git vendor/bundles/IMAG/LdapBundle
```

### Configure the Autoloader

``` php
<?php
// app/autoload.php

$loader->registerNamespaces(array(
     // ...
    'IMAG' => __DIR__.'/../vendor/bundles',
));
```

### Enable the Bundle

``` php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
    // ...
    new IMAG\LdapBundle\IMAGLdapBundle(),
    );
}
```

### Configure security.yml

``` yaml
# src/IMAG/LdapBundle/Resources/config/security.yml

security:
  firewalls:
    restricted_area:
      pattern:          ^/
      anonymous:        ~
      provider:         ldap
      imag_ldap:        ~
      logout:
        path:           /logout
        target:         /

  providers:
    ldap:
      id: imag_ldap.security.user.provider

  encoders:
    IMAG\LdapBundle\User\LdapUser: plaintext

  access_control:
    - { path: ^/login,          roles: IS_AUTHENTICATED_ANONYMOUSLY }
    - { path: ^/,               roles: IS_AUTHENTICATED_FULLY }

  factories:
    - "%kernel.root_dir%/../vendor/bundles/IMAG/LdapBundle/Resources/config/security_factories.xml"

imag_ldap:
  client:
    host: ldap://0.0.0.0
    port: 389
    version: 3
    referrals_enabled: 0
    username: <adminuser-fulldomainname>
    password: <adminpassword>

  user:
    base_dn: DC=<domain-part-1>,DC=<domain-part-2>
    name_attribute: samaccountname

  role:
    base_dn: DC=<domain-part-1>,DC=<domain-part-2>
    name_attribute: cn
    user_attribute: member
```

**You need to configure the parameters under the imag_ldap section.**

**Note:**

> If are not set, the optional parameters have default values.
> You can disable this ; Just set parameter to NULL.

``` yaml
imag_ldap:
  # ...
  role:
   # ...
   filter: NULL
```

### Import security.yml

``` yaml
# app/config/config.yml

imports:
  - { resource: ../../vendor/bundles/IMAG/LdapBundle/Resources/config/security.yml }  
```

### Import routing

``` yaml
# app/config/routing.yml

imag_ldap:
  resource: "@IMAGLdapBundle/Resources/config/routing.yml"
```

### Implement Logout

Just create a link with logout target.

``` html
<a href="{{ path('logout') }}">logout</a>
```

**Note:**
You can refer to the official Symfony documentation :
http://symfony.com/doc/2.0/book/security.html#logging-out
